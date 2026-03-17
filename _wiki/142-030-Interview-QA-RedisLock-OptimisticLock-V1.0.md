---

layout: wiki

title: 142-030-Interview-QA-RedisLock-OptimisticLock-V1.0.md

cate1: Interview

cate2: Interview

description: 142-030-Interview-QA-RedisLock-OptimisticLock-V1.0.md

keywords: Interview

---


# 面试专项准备：Redis分布式锁 + 乐观锁解决重复付款问题

<small>*基于简历亮点第4条：使用Redis分布式锁+乐观锁机制，解决高并发下的重复付款问题* | V1.0 | 崔光浩</small>

---

## 一、项目背景

### 业务场景
**财资司库平台 (Treasury Management Platform)** - 付款结算模块

**核心痛点：**
- 企业通常只有1-2个主要付款账户
- 每天几十上百笔付款需要处理
- 多个财务人员可能同时操作同一账户
- **账户余额10万元，A和B同时发起6万元付款**
- 若无并发控制，两笔付款都可能通过余额校验，导致**账户透支**和企业资金损失

---

## 二、面试官常见问题

### Q1: 能详细介绍一下这个并发问题的背景吗？

**STAR结构回答：**

**S - Situation:**
在财资司库平台的付款业务中，我们面临高并发场景。企业客户通常只有1-2个主要付款账户，但每天会有几十甚至上百笔付款需要处理。关键是**多个财务人员可能同时对同一账户操作付款**。

举个例子：账户余额10万元，上午10点A和B同时发起6万元付款。传统实现下，两个请求同时查询余额都是10万，都通过余额校验，然后同时扣减，最终导致账户透支2万元，造成企业资金损失。

**T - Task:**
我负责设计轻量级并发控制方案，需要满足三个核心要求：
1. **绝对保证**余额准确性，绝不能出现超付
2. **高性能**，不能成为系统瓶颈（目标：支持1000+ TPS）
3. **用户体验好**，付款失败后资金能立即恢复

**A - Action:**
我设计了 **"Redis分布式锁 + 乐观锁 + 预扣机制"** 的混合方案：

**第一层：Redis分布式锁**
- 使用Redisson实现
- 锁粒度：按账户加锁
- 防止同一账户的多个付款请求同时处理

**第二层：乐观锁**
```java
@Update("UPDATE account SET balance = balance - ?, data_version = data_version + 1 WHERE account_no = ? AND data_version = ?")
int deductWithVersion(String accountNo, BigDecimal amount, Long version);
```

**第三层：预扣机制（核心创新）**
- 不直接扣减余额，先创建预扣记录（冻结资金）
- 可用余额 = 当前余额 - SUM(预扣金额)
- 付款成功后才正式扣减，失败则释放预扣

**R - Result:**
这套方案上线后效果显著：
- **彻底杜绝**重复付款导致的余额超付问题（从每周5-6起降到零）
- **性能提升**：相比悲观锁SELECT FOR UPDATE，并发性能提升200%
- **成为标准**：该机制后来成为公司所有资金类操作的标准方案

---

### Q2: 为什么需要同时使用Redis分布式锁和乐观锁？

**分布式锁解决的问题：**
```java
// 问题：多个服务实例同时调用银行支付接口
// 只使用乐观锁可能导致重复支付
public void processPayment(String orderNo) {
    PaymentOrder order = paymentOrderMapper.selectByOrderNo(orderNo);
    bankService.pay(order); // 多个实例可能重复调用！
    paymentOrderMapper.updateWithVersion(order); // 乐观锁只在最后生效
}
```

**乐观锁解决的问题：**
```java
// 问题：无法保证数据库操作原子性
public void processPayment(String orderNo) {
    String lockKey = "payment:" + orderNo;
    RLock lock = redissonClient.getLock(lockKey);
    lock.lock();
    // 查询→处理→更新，但更新时可能数据已被修改
    // 需要乐观锁版本号保护
}
```

**两者结合：**
- **分布式锁**：控制"能不能做"（防止重复调用外部接口）
- **乐观锁**：保证"做得对不对"（数据一致性）
- 两者是互补关系，不是替代关系

---

### Q3: Redis分布式锁的具体实现？

**Redisson优势：**

1. **Lua脚本保证原子性**
```lua
if (redis.call('exists', KEYS[1]) == 0) then
    redis.call('hincrby', KEYS[1], ARGV[2], 1);
    redis.call('pexpire', KEYS[1], ARGV[1]);
    return nil;
end;
```

2. **看门狗自动续期**
```java
RLock lock = redissonClient.getLock("payment:account:12345");
lock.lock(); // 默认30秒过期，看门狗每10秒续期一次
```

3. **可重入机制**
```java
// 同一线程可多次获取同一把锁
public void processBatch(List<String> orderNos) {
    String lockKey = "payment:account:" + accountNo;
    RLock lock = redissonClient.getLock(lockKey);
    lock.lock();
    try {
        for (String orderNo : orderNos) {
            processSingle(orderNo); // 内部可再次获取锁
        }
    } finally {
        lock.unlock();
    }
}
```

**锁的设计：**
```java
// 按账户加锁（推荐）
public String getAccountLockKey(String accountNo) {
    return "payment:account:" + accountNo;
}
```

**性能对比：**
- 全局锁（单key）：TPS ≈ 500
- 账户级锁（account:*）：TPS ≈ 2000+
- 订单级锁（order:*）：TPS ≈ 3000+（内存占用高）

**最终选择：账户级锁** - 业务合理，性能适中

---

### Q4: 乐观锁如何实现？ABA问题怎么处理？

**数据库设计：**
```sql
CREATE TABLE account (
    id BIGINT PRIMARY KEY,
    account_no VARCHAR(32) NOT NULL,
    balance DECIMAL(18,2) NOT NULL,
    data_version BIGINT DEFAULT 0,  -- 乐观锁版本号
    INDEX idx_account_no (account_no)
);
```

**MyBatis实现：**
```java
@Mapper
public interface AccountMapper {
    @Select("SELECT * FROM account WHERE account_no = #{accountNo}")
    Account selectByAccountNo(@Param("accountNo") String accountNo);

    @Update("UPDATE account SET balance = balance - #{amount}, data_version = data_version + 1 "
          + "WHERE account_no = #{accountNo} AND data_version = #{version}")
    int deductWithVersion(@Param("accountNo") String accountNo,
                         @Param("amount") BigDecimal amount,
                         @Param("version") Long version);
}
```

**使用示例：**
```java
public void deductBalance(String accountNo, BigDecimal amount) {
    Account account = accountMapper.selectByAccountNo(accountNo);
    int updated = accountMapper.deductWithVersion(
        accountNo, amount, account.getVersion()
    );
    if (updated == 0) {
        throw new OptimisticLockException("数据已被修改，请重试");
    }
}
```

**ABA问题：**
```
场景：账户余额100元，data_version=1
T1: 线程A读取 → 余额100，version=1
T2: 线程B修改 → 余额→80，version=1→2
T3: 线程B再次修改 → 余额→100，version=2→1 (ABA发生！)
T4: 线程A更新 → 基于version=1成功更新，但实际数据已被修改过
```

**解决方案：预扣机制的状态流转**
```java
public enum FreezeStatus {
    FROZEN("已冻结"),
    DEDUCTED("已扣减"),   // 终态 - 不可逆
    RELEASED("已释放");   // 终态 - 不可逆

    public boolean canTransitionTo(FreezeStatus newStatus) {
        // FROZEN只能转到DEDUCTED或RELEASED
        // DEDUCTED和RELEASED不能再流转
        return this == FROZEN && (newStatus == DEDUCTED || newStatus == RELEASED);
    }
}
```

**为什么能解决ABA？**
1. **状态不可逆**：DEDUCTED/RELEASED是终态，不能回退到FROZEN
2. **业务逻辑保障**：只有FROZEN状态的预扣才能转为DEDUCTED
3. **时间戳辅助**：每次状态变更都有时间戳，避免使用过期数据

---

### Q5: 预扣机制如何实现？失败怎么处理？

**核心价值对比：**

**传统方式（问题）：**
```
1. 查询余额：100万
2. 判断余额 >= 付款金额？是
3. 扣减余额：100万 - 10万 = 90万
4. 调用银行接口...
5. 银行返回失败！
6. 问题：余额已扣减，需要复杂回滚
```

**预扣机制（优势）：**
```
1. 查询可用余额 = 当前余额 - 冻结总额 = 100万
2. 判断可用余额 >= 付款金额？是
3. 创建预扣记录：冻结10万
4. 可用余额 = 100万 - 10万 = 90万（实时生效）
5. 调用银行接口...
6. 银行返回失败！
7. 更新预扣状态为RELEASED
8. 可用余额立即恢复为100万
优势：无需回滚，实时恢复
```

**关键实现：**
```java
// 查询可用余额
public BigDecimal getAvailableBalance(String accountNo) {
    Account account = accountMapper.selectByAccountNo(accountNo);
    BigDecimal frozenAmount = freezeMapper.sumFrozenAmount(accountNo);
    return account.getBalance().subtract(frozenAmount);
}

// 冻结金额
@Transactional
public boolean freezeAmount(String accountNo, BigDecimal amount, String orderNo) {
    BigDecimal available = getAvailableBalance(accountNo);
    if (available.compareTo(amount) < 0) {
        return false;
    }

    PaymentFreeze freeze = new PaymentFreeze();
    freeze.setFreezeNo(generateFreezeNo());
    freeze.setAccountNo(accountNo);
    freeze.setOrderNo(orderNo);
    freeze.setAmount(amount);
    freeze.setStatus(FreezeStatus.FROZEN);
    freezeMapper.insert(freeze);

    return true;
}
```

**三种处理场景：**

1. **付款成功**
```java
// 1. 正式扣减余额（乐观锁）
accountMapper.deductWithVersion(accountNo, amount, version);

// 2. 更新预扣状态
freeze.setStatus(FreezeStatus.DEDUCTED);
freezeMapper.update(freeze);

// 3. 更新订单状态
order.setStatus("PAID");
orderMapper.update(order);
```

2. **付款失败**
```java
// 1. 释放预扣（状态改为RELEASED）
freeze.setStatus(FreezeStatus.RELEASED);
freezeMapper.update(freeze);

// 2. 更新订单状态
order.setStatus("FAILED");
order.setFailReason(failReason);
orderMapper.update(order);

// 3. 可用余额自动恢复！
```

3. **付款超时**
```java
// 定时任务：每10分钟扫描一次
@Scheduled(cron = "0 */10 * * * ?")
public void releaseTimeoutFrozen() {
    // 查询30分钟前仍处于FROZEN状态的记录
    LocalDateTime timeoutTime = LocalDateTime.now().minusMinutes(30);
    List<PaymentFreeze> timeoutList = freezeMapper.findByStatusAndCreateTime(
        FreezeStatus.FROZEN, timeoutTime
    );

    // 批量释放
    for (PaymentFreeze freeze : timeoutList) {
        freeze.setStatus(FreezeStatus.RELEASED);
        freezeMapper.update(freeze);
    }
}
```

---

### Q6: 并发性能如何？有哪些优化？

**性能测试数据：**

| 方案 | TPS | 平均响应时间 | 成功率 | 备注 |
|------|-----|-------------|--------|------|
| 悲观锁SELECT FOR UPDATE | 150 | 650ms | 96% | 性能差 |
| 仅Redis分布式锁 | 450 | 220ms | 99% | 一般 |
| 仅乐观锁 | 800 | 125ms | 92% | 重试率高 |
| Redisson锁+乐观锁 | 650 | 154ms | 99.5% | 安全 |
| **我们的方案（+预扣）** | **2200** | **45ms** | **99.9%** | **最优** |

**优化措施：**

**优化1：Redis缓存冻结金额**
```java
public BigDecimal getFrozenAmountWithCache(String accountNo) {
    String cacheKey = "account:frozen:" + accountNo;
    String cached = redisTemplate.opsForValue().get(cacheKey);

    if (cached != null) {
        return new BigDecimal(cached);
    }

    // 缓存未命中，查询数据库
    BigDecimal frozenAmount = freezeMapper.sumFrozenAmount(accountNo);

    // 写入缓存
    redisTemplate.opsForValue().set(
        cacheKey, frozenAmount.toString(), 5, TimeUnit.MINUTES
    );

    return frozenAmount;
}
```
效果：查询从15ms → 1ms，TPS提升30%

**优化2：批量处理**
```java
// 批量插入预扣记录（一次数据库交互）
@Transactional
public boolean batchFreeze(String accountNo, List<BigDecimal> amounts) {
    BigDecimal totalAmount = amounts.stream().reduce(BigDecimal.ZERO, BigDecimal::add);
    BigDecimal available = getAvailableBalance(accountNo);

    if (available.compareTo(totalAmount) < 0) {
        return false;
    }

    // 批量插入
    List<PaymentFreeze> freezeList = amounts.stream().map(amount -> {
        PaymentFreeze freeze = new PaymentFreeze();
        freeze.setFreezeNo(generateFreezeNo());
        freeze.setAccountNo(accountNo);
        freeze.setAmount(amount);
        return freeze;
    }).collect(Collectors.toList());

    freezeMapper.batchInsert(freezeList);
    return true;
}
```

**优化3：异步化处理**
```java
// 同步：800ms
// 异步：50ms（仅受理）
// 吞吐量提升：16倍

public Result submitPaymentAsync(PaymentRequest request) {
    // 快速预扣（同步）
    boolean frozen = freezeAmount(request.getAccountNo(), request.getAmount());
    if (!frozen) {
        return Result.fail("余额不足");
    }

    // 创建订单
    String orderNo = createOrder(request);

    // 发送异步消息
    kafkaTemplate.send("payment-topic", orderNo, event);

    // 立即返回
    return Result.success(orderNo);
}
```

**优化4：数据库索引**
```sql
CREATE TABLE payment_freeze (
    -- 核心查询索引
    INDEX idx_account_status (account_no, status),
    INDEX idx_order (order_no),
    INDEX idx_status_create (status, create_time)
);
```
效果：查询扫描行数从100万行 → 100行

**优化5：连接池调优**
```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
```

---

### Q7: 方案有什么局限性？怎么优化？

**局限性1：最终一致性时间窗口**
```
预扣和实际扣减之间存在时间窗口（银行处理时间）
这段时间内，余额和可用余额不一致

影响：对账时需要处理中间状态，查询余额显示"可用余额"
```

**局限性2：热点账户瓶颈**
```
大型企业通常只有1-2个主要付款账户
这些账户成为热点，预扣记录查询压力大

数据：热点账户日均5000+笔预扣，查询从10ms → 200ms
```

**局限性3：批量付款复杂度**
```
批量付款1000笔，部分成功场景处理复杂
需要精确计算成功金额、失败金额
状态不一致风险增加
```

**系统规模扩大10倍（500万笔/日）的优化：**

**优化1：分库分表**
```java
// 按账户号哈希分片（16个分片）
public String determineTable(String accountNo) {
    int hash = accountNo.hashCode();
    int tableIndex = Math.abs(hash) % 16;
    return "account_" + tableIndex;
}
```
效果：单表从5000万行 → 300万行，查询性能提升3-5倍

**优化2：Redis集群+RedLock**
```java
// 跨数据中心Redis集群
RedissonRedLock redLock = new RedissonRedLock(
    redissonClientBJ.getLock("lock"),  // 北京
    redissonClientSZ.getLock("lock"),  // 深圳
    redissonClientSH.getLock("lock")   // 上海
);
```

**优化3：消息队列+事件驱动**
```java
// 架构演进：同步 → 异步
同步：请求 → 处理 → 返回（800ms）
异步：请求 → 受理 → 返回（50ms）→ 异步处理
```

**优化演进路线：**
```
当前架构（50万笔/日）
    ↓
缓存+索引优化（100万笔/日）
    ↓
批量+异步化（200万笔/日）
    ↓
分库分表+Redis集群（500万笔/日）
    ↓
消息队列+事件驱动（1000万笔/日）
    ↓
单元化架构（1亿+笔/日）
```

---

## 三、关键代码示例

```java
/**
 * 付款核心服务
 */
@Service
@Slf4j
public class PaymentService {

    private final RedissonClient redissonClient;
    private final AccountMapper accountMapper;
    private final PaymentFreezeMapper freezeMapper;
    private final BankService bankService;

    @Transactional
    public PaymentResult processPayment(PaymentRequest request) {
        String accountNo = request.getAccountNo();
        BigDecimal amount = request.getAmount();
        String orderNo = generateOrderNo();

        RLock lock = null;
        boolean lockAcquired = false;

        try {
            // 1. 获取分布式锁
            String lockKey = "payment:" + accountNo;
            lock = redissonClient.getLock(lockKey);
            lockAcquired = lock.tryLock(5, 30, TimeUnit.SECONDS);

            if (!lockAcquired) {
                return PaymentResult.fail("系统繁忙，请稍后重试");
            }

            // 2. 冻结金额
            boolean frozen = freezeAmount(accountNo, amount, orderNo);
            if (!frozen) {
                return PaymentResult.fail("余额不足");
            }

            // 3. 调用银行接口
            BankResult bankResult = bankService.pay(orderNo, amount);

            // 4. 处理结果
            if (bankResult.isSuccess()) {
                handlePaymentSuccess(orderNo);
                return PaymentResult.success(orderNo);
            } else {
                handlePaymentFailure(orderNo, bankResult.getFailReason());
                return PaymentResult.fail("付款失败：" + bankResult.getFailReason());
            }

        } catch (Exception e) {
            log.error("付款处理异常", e);
            return PaymentResult.fail("系统异常");
        } finally {
            if (lockAcquired && lock != null) {
                lock.unlock();
            }
        }
    }

    /**
     * 冻结金额
     */
    private boolean freezeAmount(String accountNo, BigDecimal amount, String orderNo) {
        // 查询账户
        Account account = accountMapper.selectByAccountNo(accountNo);
        BigDecimal frozenAmount = freezeMapper.sumFrozenAmount(accountNo);
        BigDecimal available = account.getBalance().subtract(frozenAmount);

        // 余额检查
        if (available.compareTo(amount) < 0) {
            return false;
        }

        // 创建预扣记录
        PaymentFreeze freeze = new PaymentFreeze();
        freeze.setFreezeNo(generateFreezeNo());
        freeze.setAccountNo(accountNo);
        freeze.setOrderNo(orderNo);
        freeze.setAmount(amount);
        freeze.setStatus(FreezeStatus.FROZEN);
        freezeMapper.insert(freeze);

        return true;
    }
}
```

---

## 四、面试回答要点

### STAR法则
- **S**: 财资司库平台高并发付款场景，多用户操作同一账户
- **T**: 设计轻量级并发控制方案，保证余额准确、高性能、用户体验好
- **A**: Redis分布式锁 + 乐观锁 + 预扣机制的混合方案
- **R**: 重复付款降到零，性能提升200%，成为公司标准

### 技术亮点
1. **深入研究Redisson源码** - 理解Lua脚本原子性、看门狗机制
2. **架构权衡** - 对比多种方案，选择适合中小企业的轻量级方案
3. **量化成果** - TPS 2200，响应时间45ms，成功率99.9%
4. **完整性** - 覆盖正常流程、异常处理、超时补偿、批量处理

### 深度追问准备
- 为什么需要两种锁？（作用在不同层面）
- Redis锁key怎么设计？（按账户，细粒度）
- ABA问题怎么解决？（预扣机制状态流转）
- 付款失败资金怎么恢复？（更新为RELEASED立即释放）
- 批量付款部分成功怎么处理？（精确计算分别处理）

---

## 五、相关文档

- [Redis分布式锁与乐观锁使用场景](./../../../149-Resource-资源/149-001-Lock-乐观锁和%20Redis%20分布式锁使用场景.md)
- [分布式事务一致性设计](./../142-010-Java-财资司库业务需求梳理/015分布式事务一致性设计-账户结算与审批模块.md)
- [余额并发控制设计](./../142-010-Java-财资司库业务需求梳理/016余额并发控制设计-预扣机制解决超付问题.md)

---

**文档版本：** V1.0
**最后更新：** 2026-03-15
**维护：** 崔光浩
