---

layout: wiki

title: My Resume 2023

cate1: Tools

cate2: Editor

description: My Resume 2023

keywords: Resume

---

# Interview_Java_总结篇


11月17日

#### 面试问题列表

熔断是在调用方还是服务方 配置？
项目中实际怎样使用的 synchronized 锁？
Vue 面试题？
Spring 分布式事务原理？
项目中使用的 GC 方案？
dump 文件分析主要关注哪些参数？
ES的分词器，IK分词器了解吗？
Redis什么是大Key问题，如何解决的？
```
问题：全栈中cmdb sdk报错：save labels props to memory is error. cloud not get a resource from the pool
原因：redis某慢查询语句导致单个节点超时导致主从切换
    IP工作台现场：SCAN match bit_matrix:LABELSPROPS:** 耗时近18s，但现场集群超时配置为5s cluster-node-timeout=5000
解决：修改redis连接超时配置,cluster-node-timeout=15000，优化代码中redis keys * 查询逻辑
```
Spring @Transactional 事务使用时，使用过那些事务传播特性？
保存资源和资源类型之间的关系，需要保证事务。
参考：[2成的业务代码的Spring声明式事务，可能都没处理正确](https://learn.lianglianglee.com/%e4%b8%93%e6%a0%8f/Java%20%e4%b8%9a%e5%8a%a1%e5%bc%80%e5%8f%91%e5%b8%b8%e8%a7%81%e9%94%99%e8%af%af%20100%20%e4%be%8b/06%202%e6%88%90%e7%9a%84%e4%b8%9a%e5%8a%a1%e4%bb%a3%e7%a0%81%e7%9a%84Spring%e5%a3%b0%e6%98%8e%e5%bc%8f%e4%ba%8b%e5%8a%a1%ef%bc%8c%e5%8f%af%e8%83%bd%e9%83%bd%e6%b2%a1%e5%a4%84%e7%90%86%e6%ad%a3%e7%a1%ae.md)
Kafka 消息丢失问题
- 对于第一次纳管的数据
  - 超时提示
- 对于周期性采集的数据
  - 重试
    `Kafka` 、 `RabbitMQ` 区别？
    Redis可以用来做什么用：唯一ID、乐观锁
    JMM：内存模型
    Voltain关键字：共享变量可见性，有序性，禁之指令重排。
    OOM、STW
    内存泄漏
    IO密集
    网管gateway拦截器
    Controller是单例？
    Mysql慢查询排查过吗？
    feign调用涉及到哪些注解？
    多线程如何异步回调获取结果？
    Redis配置RDB和AOF命令
    kafka如何保证消息有序？
    集合中数据去重？
    HAVING 和 GROUP BY区别？子查询需要 AS subQuery。
    MyBatis原理和流程？
    Docker常用命令？
    MyBatis：
* 一级二级缓存
* 分页方式有哪几种
  Spring Bean注入方式
  如何对集合排序？如何使用stream对集合排序？
  了解和使用过反射吗？：扩展程序热加载Class文件
  MyBatis中引用对象如何查询，如何映射到对象上？多对多关系如何映射的？
  GateWay登录流程？
  Kafka多副本机制，以及可能存在的问题？
  Kafka自动提交和手动提交有什么区别？
  Sentinel限流是怎么处理的？
  MyBatis使用的基本步骤？
  实际使用的代码自动生成工具？
  模版和算法区别?
  了解hash算法吗

Spring
- 微服务化时如何对服务进行拆分？
- SpringCloud各个组件使用过吗？

MySQL
- 查询总分大于100的学生和每一科都超过60分的学生

**MySQL 索引模型**

- InnoDB 使用了 B+ 树索引模型。
  聚簇索引（clustered index）：主键索引的叶子节点存的是整行数据。在 InnoDB 里，主键索引也被称为聚簇索引（clustered index）。
  二级索引（secondary index）：非主键索引的叶子节点内容是主键的值。在 InnoDB 里，非主键索引也被称为二级索引（secondary index）。

普通索引查询方式需要回表。

索引优化，如何避免回表?
* 覆盖索引可以减少树的搜索次数，显著提升查询性能

[04 深入浅出索引（上）](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/04%20%20%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BA%E7%B4%A2%E5%BC%95%EF%BC%88%E4%B8%8A%EF%BC%89.md)
[05 深入浅出索引（下）](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/05%20%20%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BA%E7%B4%A2%E5%BC%95%EF%BC%88%E4%B8%8B%EF%BC%89.md)


SQL查询语句

学生成绩前三

```sql
select s.id, e.id
    from student s, exam e
    where s.eId = e.id 
    order by e.score  desc
    limit 3;
```

优化

```sql
select 
    s.id, e.id
from 
    student s
    left join exam e on s.eId = e.id
order by 
    e.score  desc
limit 3;
```


**优化**

反射
设计模式
VUE


**Redis**

五大数据类型：String、List、Set、ZSet、Hash
五大数据结构：整数数组、链表、Hash、压缩链表、跳表
Redis用途：

* 作为缓存

* 作为消息队列
  * 消息保序：使用List类型，LPUSH、RPOP（BRPOP阻塞式读取）
  * 重复消息：消息提供全局唯一的 ID 号
  * 可靠传输：List类型是如何保证消息可靠性，BRPOPLPUSH

* 大数据量：使用二级编码的方法，实现了用集合类型保存单值键值对，Redis 实例的内存空间消耗明显下降了

**Kafka**

传输消息的格式: Kafka使用的是纯二进制的字节序列。

传输协议：

* 点对点模型
* 发布 / 订阅模型

Kafka 名词术语：
- 消息：Record。Kafka 是消息引擎嘛，这里的消息就是指 Kafka 处理的主要对象。
- 主题：Topic。主题是承载消息的逻辑容器，在实际使用中多用来区分具体的业务。
- 分区：Partition。一个有序不变的消息序列。每个主题下可以有多个分区。
- 消息位移：Offset。表示分区中每条消息的位置信息，是一个单调递增且不变的值。
- 副本：Replica。Kafka 中同一条消息能够被拷贝到多个地方以提供数据冗余，这些地方就是所谓的- 副本。副本还分为领导者副本和追随者副本，各自有不同的角色划分。副本是在分区层级下的，即每个分区可配置多个副本实现高可用。
- 生产者：Producer。向主题发布新消息的应用程序。
- 消费者：Consumer。从主题订阅新消息的应用程序。
- 消费者位移：Consumer Offset。表征消费者消费进度，每个消费者都有自己的消费者位移。
- 消费者组：Consumer Group。多个消费者实例共同组成的一个组，同时消费多个分区以实现高吞吐。
- 重平衡：Rebalance。消费者组内某个消费者实例挂掉后，其他消费者实例自动重新分配订阅主题分区的过程。Rebalance 是 Kafka 消费者端实现高可用的重要手段。

持久化：Kafka 使用消息日志（Log）来保存数据。


Kafka 保证消息不丢失：

* Producer：使用带有回调通知的发送 API
* Consumer：不要开启自动提交位移，而是要应用程序手动提交位移

Kafka 保证消息准确性：

* Producer：开启幂等配置 enable.idempotence = true。

* Producer： 事务型 Producer
  * 开启 enable.idempotence = true。
  * 设置 Producer 端参数 transctional.id
  * Consumer 端设置 isolation.level=read_committed

Rebalance触发条件：

* 组成员数发生变更。
* 订阅主题数发生变更。
* 订阅主题的分区数发生变更。


**MySQL**

MySQL 脏读、不可重复读、幻读： [MySQL中的幻读，你真的理解吗？](https://cloud.tencent.com/developer/article/1593481)

[查询总分大于100的学生和每一科都超过60分的学生](https://blog.csdn.net/qq_36004234/article/details/123522933)

![img](https://onecup-image.oss-cn-beijing.aliyuncs.com/imgs/typora/202311070847359.png)

## 自我介绍

### My自我介绍

领导您好，我叫xxx。有4年的开发经验。上家公司是在xxx公司，最近做的项目是资源管理与全栈监控平台。主要是 支撑 IT、CT 相关资源的采集、监控、告警。平台基于SpringCloud搭建，使用到Eureka、Apollo、Kafka、Redis、ES等。我个人在其中负责的是 资源资产管理 模块的产品，负责核心业务代码编写，指导新同事，参与线上问题的定位和调优。我的大致情况是这样。

### 个人优势

拥有4年 Java  后端开发经验。熟练掌握 Spring、SpringCloud、MyBatis  等主流框架，具备扎实的 Java  知识和优秀的问题解决能力。在中大型项目中担任过核心研发角色，进行过系统性能优化和稳定性提升。具有良好的团队合作精神和持续学习的态度，能够快速适应新技术和挑战。


### 有什么想问的

技术：

* 项目或产品中使用到的技术，是微服务吗
* 产品项目进度或所处阶段，以及未来1年的规划。
* 小组整体氛围，平时加班情况
* 有没有什么技术培训项目？贵公司的晋升机制是什么样的？贵公司业务及战略的未来发展？
* 公司文化是什么？
*

HR：

* 有13薪或年终奖吗？
* 公司规模或公司发展方向？
* 平时加班情况？加班车费报销？
* 假期福利

### 你的优点和缺点是什么？

优点：
* 热爱技术，喜欢分享
* 有责任感、细心

缺点：
* 文档编写，效率不如编码时高
* 语言表达能力不够强

### 在这里工作你喜欢什么？

我从我所做的工作中获得了满足。
我喜欢与真正聪明，友好的同事合作。
尊重技术。

### 讲个笑话？

“Knock, knock. Who’s there? Hire. Hire who? Hire me！”



# 资源配置监控平台

#### 主要数据源

IT资产：
* Linux、Windows、Tomcat、MySQL、Kafka、Redis
  * 资源信息：
    * Linux：CPU核数、内存总量、操作系统
    * 文件系统：挂载点、所属逻辑卷
  * 监控信息：
    * Linux：CPU利用率、内存利用率、Ping状态
    * MySQL：会话数、连接状态
* VMware、华为MO6、华为云、中兴云
  * VMware云、VMware虚拟机、物理机、虚拟磁盘网卡
* 运营商的5G消息、5G短信网关、骨干网、核心网
* 路由器、交换机、网络接口
* 小型机、机架服务器
* 平板电脑、打印机



### 资产监控项目

#### 产品亮点

CMDB配置资源管理亮点：

1. **全面的配置管理功能**：提供了包括模型管理、资源管理、拓扑管理在内的全方位配置管理能力。
2. **高效的资源管理和检索**：实现了高效的资源管理系统，包括多角度资源视图和全属性资源检索。
3. **可视化的拓扑管理**：实现了资源和业务架构的可视化拓扑管理。
4. **自动化采集和智能化的运维监控支持**：通过自动化采集和智能监控，提升了运维效率。
5. **先进的技术栈**：使用 Java、SpringCloud、MyBatis、Feign 等成熟技术栈。
6. **显著的客户价值**：优化了应用架构，提高了告警监控效率。

* 使用Redis作为缓存，减小数据库访问压力。
* Elasticsearch作为搜索引擎搭建搜索平台，支持对所有字段的搜索。
* 使用Kafka作为消息队列，来减小服务间耦合，填谷削峰。
* 资源间关系的处理
* 资源权限管理
* 多租户管理
* 资源告警规则配置，资源变更通知
* 资源变更操作历史
*
* Eureka作为注册中心，Feign远程调用，Apollo作为配置中心
* 对标蓝鲸监控、Prometheus
* 前后端分离VUE
* Jenkins 持续集成

* CMDB-SDK，提供第三方集成工具SDK
*

低效无效资产清理：
IP地址管理：

#### 项目难点：

1. **复杂的系统集成**：整合多个 IT 管理和运维系统。
2. **高效的数据处理**：处理大量的配置数据。
3. **资源模型的灵活性和扩展性**：设计适应多种业务需求的资源模型。
4. **系统性能和稳定性**：在高并发和大数据量下保持性能。
5. **安全性和权限管理**：实现严格的安全措施和细粒度的权限控制。

* 数据量大，使用Kafka作为消息队列。

* 数据量大，业务处理慢：
  * 优化业务逻辑，减少查询数据库，改为查询redis和es
  * 多线程处理
  * 增加ehcache缓存
  * 合并kafka消息
  * 保证服务器批量纳管成功
  * 多个服务间消息准确传递

**项目难点**

- 首先他要解决的问题是，资源资产配置管理与监控。公司的资产资源配置没有统一管理，统一监控。
- 复杂性在于系统间数据传输，大数据量采集处理。
- 最终的结果是，保证系统高可用。


#### 工作成就

工作成就：
- **系统性能优化**：通过优化数据库查询和缓存机制，减少了系统的响应时间，提高了用户体验。
- **代码重构**：定期进行代码审查和重构，提高了代码的可读性和可维护性。通过移除冗余代码和优化现有功能，提高了系统的整体性能和可靠性。
- **安全性和权限控制**：实现基于角色的访问控制（RBAC），定期安全审计。
- **文档和知识共享**：编写详尽的技术文档和开发指南，促进了团队内的知识共享，为新团队成员的快速上手提供了便利。
- **技术指导和团队合作**：在项目中担任技术指导角色，帮助团队成员解决技术难题，促进了团队内的技术交流和合作。
- **自动化测试和部署**：引入自动化测试和部署流程，确保了代码的质量和稳定性。通过持续集成/持续部署(CI/CD)管道，加快了开发周期，减少了部署中的错误。


解决OOM问题：

* 查询数据量过大，未分页导致OOM（查询资产名称，使用List接收，数据量大时导致OOM）

优化数据库查询：

* 联表查询，CQL查询数据量过大，增加查询条件
* CQL未使用索引，新建索引解决

提升系统整体性能
提高吞吐量
提高系统稳定性

对采集的资源消息单独的业务处理 优化为使用策略模式 实现通用处理逻辑


#### 技术在项目中的应用

##### redis

* Redis在项目中把部分热点类型的数据全量放到redis中，作为缓存数据库使用。如Linux、MySQL、VMWare等采集的资源。
* 非热点类型的数据设置缓存过期时间。如平板电脑、打印机等资产。

##### MySQL

分库分表

你的项目里该如何分库分表？一般来说，垂直拆分，你可以在表层面来做，对一些字段特别多的表做一下拆分；

**垂直拆分**的意思，就是**把一个有很多字段的表给拆分成多个表**，**或者是多个库上去**。（把一个大表拆开，订单表、订单支付表、订单商品表。）


#### 组件部署

**Redis 集群部署**
- **服务器**：3台。
- **部署方式**：每台服务器运行两个Redis实例，配置为三主三从。
- **配置**：主从复制，持久化（RDB或AOF）。
- **高可用**：使用Redis Sentinel进行故障转移。

**Elasticsearch 集群部署**
- **服务器**：3台。
- **部署方式**：每台服务器运行一个Elasticsearch节点。
- **配置**：所有节点同一集群，节点间复制和分片。
- **高可用**：主节点选举，副本分布在不同节点。

**Kafka 集群部署**
- **服务器**：3台。
- **部署方式**：每台服务器运行一个Kafka broker。
- **配置**：所有broker连接同一个Zookeeper集群。
- **高可用**：数据副本分布在不同broker，分区多副本。


# 面试题


#### Spring

##### 什么是 Spring IOC 容器？

Spring IOC 容器是 Spring 的核心组件，负责管理 Java 应用中的对象。它通过控制反转（IOC）机制，将对象的创建和管理从程序代码转移到容器中，实现了对象的依赖注入和生命周期管理。

简化记忆：Spring IOC 容器管理对象，实现创建、注入、管理，减少代码耦合。

##### 什么是依赖注入（DI）？可以通过多少种方式完成依赖注入？

依赖注入（DI）是一种编程技术，其中一个对象提供另一个对象的依赖项。
在 Java 的 Spring 框架中，依赖注入可以通过三种主要方式完成：

1. **构造器注入**：通过对象的构造器传递依赖。
2. **Setter 注入**：通过对象的 Setter 方法注入依赖。

简化记忆：依赖注入减少耦合，通过构造器、Setter 注入方式实现。

##### 2. 如何实现一个Spring容器

1、配置文件配置包扫描路径
2、递归包扫描获取.class文件
3、反射、确定需要交给IOC管理的类
4、对需要注入的类进行依赖注入

##### 什么是 AOP？

AOP（面向切面编程）是一种编程技术，它允许将跨越多个点的功能（如日志、安全等）与主业务逻辑分离，提高了代码的模块化。

简化记忆：AOP 分离横切关注点，如日志和安全，通过切面、切点和通知改善代码结构。

##### AOP 的核心概念

1. **切面（Aspect）**：横切关注点的模块化，例如日志记录等。
2. **连接点（Join Point）**：程序执行的某个特定位置，如方法的调用。
3. **切入点（Pointcut）**：匹配连接点的断言，在这里实际插入切面的地方。
4. **通知（Advice）**：Before、After、环绕通知（Around）、After returning、After throwing等。

```java
@Aspect  
@Component  
public class UpdateIpmngResAOP {
	@Pointcut("execution(public * com.ultra.cmdb.ServiceImpl.updateResNode(..))")  
	private void updateResNode() {  
	}  
	@Around("updateResNode()")  
	public Object around(ProceedingJoinPoint pjp) throws Throwable {
	
```

Spring 框架中使用了多种设计模式，主要包括：

1. **单例模式**：在 Spring 中，默认情况下，所有的 Bean 都是以单例模式创建的，即每个 Bean 定义对应一个实例。
2. **代理模式**：Spring AOP 使用代理模式来创建方法拦截。
3. **模板方法模式**：如 `JdbcTemplate`、`RedisTemplate`。
4. **装饰器模式**：Spring 中 `BufferedReader` 和 `BufferedWriter` 是对 `Reader` 和 `Writer` 的装饰。
5. **工厂模式**：`BeanFactory` 接口定义了 Spring 的 Bean 工厂，用于创建 Bean 对象。
6. **适配器模**：Spring MVC 中，`HandlerAdapter` 用于适配不同类型的 Controller。
   Spring 定义了统一的接口 HandlerAdapter，并且对每种 Controller 定义了对应的适配器类。这些适配器类包括：AnnotationMethodHandlerAdapter、SimpleControllerHandlerAdapter、SimpleServletHandlerAdapter 等。
7. 职责链模式在 Spring 中的应用是拦截器（Interceptor）。
8. 代理模式经典应用是 AOP。
   参考: [开源实战四（下）：总结Spring框架用到的11种设计模式](https://time.geekbang.org/column/article/238418)

##### Spring 事务实现方式有哪些以及原理？

Spring 提供了两种事务管理方式，声明式和编程式：

- **声明式事务**：主要通过 AOP 实现，便于使用，减少了代码侵入性。
- **编程式事务**：

**声明式事务原理**：

1. 当一个被 `@Transactional` 标注的方法被调用时，Spring 会创建一个事务。
2. 如果方法正常完成，事务将被提交；如果方法抛出异常，则事务将被回滚。
3. Spring 使用 AOP 代理来拦截方法调用，并根据 `@Transactional` 注解的属性来决定如何开启、提交或回滚事务。

##### Spring 中的事务隔离级别包括：

1. **READ_UNCOMMITTED**
2. **READ_COMMITTED**
3. **REPEATABLE_READ**
4. **SERIALIZABLE**
5. **DEFAULT**：使用数据库默认隔离级别。

```java
@Transactional(isolation = Isolation.READ_COMMITTED，
			   propagation = Propagation.REQUIRED, 
			   rollbackFor = Exception.class)
public void myTransactionalMethod() {
```


> 脏读（Dirty reads）——脏读发生在一个事务读取了另一个事务改写但尚未提交的数据时。如果改写在稍后被回滚了，那么第一个事务获取的数据就是无效的。
> 不可重复读（Nonrepeatable read）——不可重复读发生在一个事务执行相同的查询两次或两次以上，但是每次都得到不同的数据时。这通常是因为另一个并发事务在两次查询期间进行了更新。
> 幻读（Phantom read）——幻读发生在一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录。

##### Spring事务定义的传播规则

1. **REQUIRED**：如果存在当前事务，加入该事务；否则创建新事务。
2. **SUPPORTS**：如果存在当前事务，加入该事务；否则以非事务方式执行。
3. **MANDATORY**：如果存在当前事务，加入该事务；否则抛出异常。
4. **REQUIRES_NEW**：始终创建新事务，如果存在当前事务，将其挂起。
5. **NOT_SUPPORTED**：始终非事务方式执行，如果存在当前事务，将其挂起。
6. **NEVER**：始终非事务方式执行，如果存在当前事务，抛出异常。
7. **NESTED**：如果存在当前事务，创建嵌套事务；否则行为同 REQUIRED。

##### Spring事务什么时候会失效?

1. **非公共方法**：`@Transactional` 用于非公共方法时可能失效。
2. **内部方法调用**：同一类中的非事务方法内部调用事务方法时可能失效。
3. **异常处理**：只有运行时异常默认回滚，检查型异常不回滚，除非显式配置。
4. **数据源/数据库支持**：使用的数据库或数据源不支持事务。

##### 事务有四个特性：ACID

* 原子性（Atomicity）
* 一致性（Consistency）
* 隔离性（Isolation）
* 持久性（Durability）

##### 什么是springmvc拦截器以及如何使用它？

Spring MVC 拦截器用于在请求处理前后进行额外的处理。

1. **定义拦截器**：实现 `HandlerInterceptor` 接口或继承 `HandlerInterceptorAdapter` 类，重写其中的方法（`preHandle`、`postHandle`、`afterCompletion`）。
2. **注册拦截器**：在 Spring 配置中注册拦截器，通常是在一个实现了 `WebMvcConfigurer` 的配置类中重写 `addInterceptors` 方法。


##### SpringBoot在项目中都使用到哪些注解？

**@SpringBootApplication**：
**@Transactional**：
**@Aspect**：
**@Around**
**@Scheduled**：
**@Cacheable**, **@CachePut**, **@CacheEvict**：
**@Conditional**：


##### 自定义注解的使用

```java
// 定义注解
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.TYPE})
public @interface RedisLockAnnotation {
    String LOCK_KEY();
    int LOCK_TIME() default 1800;
    boolean NEED_UNLOCK() default false;}

// 通过AOP监听注解
@Aspect
public class RedisLockHander {
    @Pointcut("@annotation(com.ultra.cmdb.annotation.RedisLockAnnotation)")
    public void redisLock() {
    
    @Around("redisLock()")
    public Object lock(ProceedingJoinPoint pjp) throws Throwable {
            MethodSignature signature = (MethodSignature) pjp.getSignature();
            Method method = signature.getMethod();
            // 获取注解
            RedisLockAnnotation annotation = method.getAnnotation(RedisLockAnnotation.class);
            lockKey = annotation.LOCK_KEY();
                if (needUnlock) {              //需要锁
                  hasLock = redisLockUtil.lock(lockKey, 10 * 60);

    // 瞬时锁，如果获取不到锁 立刻返回获取锁失败
    public boolean lock(String key, int seconds) {
          String address = getIpAddress();
        String result = jedis.set(lockKey, address, new SetParams().nx().ex(seconds));

    // 同步所有厂商入库
    @RedisLockAnnotation(LOCK_KEY = "SYNCFIRMMODEL", LOCK_TIME = 24 * 60 * 60)
    public void sysAllFirm() {
```


##### 如何使用 Spring Boot 实现全局异常处理？

使用 `@ControllerAdvice` 和 `@ExceptionHandler` 注解处理全局异常。

```java
@ControllerAdvice
@ResponseBody
public class GlobalExceptionHandler {
    @ExceptionHandler(value = Exception.class)
    public ResponseEntity exceptionHandler(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod, Exception e) {
      printStackTrace(request, handlerMethod, e);
        
        ResponseEntity<Object> resEnt = new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
        return resEnt;
    }
}
```


##### 文件上传下载@注解是什么？

MultipartFile

```java
// 使用 MultipartFile 接收文件
@RequestParam("file") MultipartFile file,
// 将文件上传到服务器
orgFile.transferTo(toFile);
AttachFile attFile = new AttachFile();
// 由文件读出数据，处理
is = new FileInputStream(f);

/** 导入 **/
dataMaps = ImportUtil.getDataListFromExcel(is, pMap);
// 处理一个excel
XSSFWorkbook workbook = new XSSFWorkbook(is);
handleOneSheet(workbook, sheetNum, pMap) {
// 处理一个sheet
  Sheet sheet = workbook.getSheetAt(sheetNum);
  Row row = sheet.getRow(0);
  // 处理一个单元格
  Cell cell = row.getCell(colName);
  date = cell.getDateCellValue();
}
/** 导出 **/
ExportUtil.exportResFailsExcel(){
  workbook = new SXSSFWorkbook(rowAccessWindowSize);
  sheet = workbook.createSheet(sheetName);
  row = sheet.createRow(0);
  cell = row.createCell(i);
```


### MyBatis

##### Mybatis中#和$的区别？

\#占位符是使用预编译的方式进行参数传递，防止SQL注入攻击。

##### Mybatis中一级缓存与二级缓存？

一级缓存是会话级别的，二级缓存是跨会话的。


### JUC

##### ThreadPoolExecutor对象有哪些参数？都有什么作用？

1. **核心线程数（corePoolSize）**：池中保留的线程数，即使它们处于空闲状态。
2. **最大线程数（maximumPoolSize）**：池中允许的最大线程数。
3. **保持活动时间（keepAliveTime）**：当线程数大于核心线程数时，这是多余空闲线程在终止前等待新任务的最长时间。
4. **时间单位（unit）**：`keepAliveTime` 的时间单位。
5. **工作队列（workQueue）**：用于在执行任务之前保存任务的队列。
6. **线程工厂（threadFactory）**：用于创建新线程的工厂。
7. **拒绝策略（handler）**：当执行器无法接受新任务时使用的策略。


##### 常见线程安全的并发容器有哪些？

**ConcurrentHashMap**：采用分段锁的方式实现线程安全
**CopyOnWriteArrayList、CopyOnWriteArraySet**：采用写时复制实现线程安全

##### 线程池大小设置?

1. **CPU 密集型任务**：线程池大小建议设置为 CPU 核心数量加 1。
2. **IO 密集型任务**：线程池大小可设置为 CPU 核心数量的两倍，因为 IO 操作不会一直占用 CPU。

### Java

#### Java8 新特性

##### Stream常用方法

map，filter，forEach，limit/skip，distinct

* 遍历操作(map)：.stream().map().collect()
* 过滤操作(filter)：.stream().filter() .collect()
* 循环操作(forEach): .stream().forEach()
* 返回特定的结果集合（limit/skip）：.stream().skip().limit().collect()
* 排序（sort/min/max/distinct）：.stream().distinct().collect()

#### JavaSE

##### 接口与抽象类的区别

![img](https://onecup-image.oss-cn-beijing.aliyuncs.com/imgs/typora/202312231811241.png)

##### Java创建内部类的方式？

非静态内部类

```java
public class Outerclass {
  public  class innerclass{
        public void  to() {
            System.out.println(name);
        }
    }
  public static void main(String[] args) {
        Outerclass q = new Outerclass();
        Outerclass.innerclass c = q.new innerclass();
        c.to();
    }
}
```



静态内部类

```java
public class Outerclass {
    public static  class innerclass {
        public void  to() {
            System.out.println(name);
        }
    }
    public static void main(String[] args) {
        innerclass q = new innerclass();
    }
 
}
```



##### Synchronized 修饰普通方法和静态方法有什么区别？

Synchronized修饰非静态方法，实际上是对调用该方法的对象加锁，俗称“对象锁”。

Synchronized修饰静态方法，实际上是对该类对象加锁，俗称“类锁”。



##### synchronized和ReentrantLock有什么区别呢？

synchronized：提供了互斥的语义和可见性，当一个线程已经获取当前锁时，其他试图获取的线程只能等待或者阻塞在那里。

ReentrantLock：再入锁，它是表示当一个线程试图获取一个它已经获取的锁时，这个获取动作就自动成功，这是对锁获取粒度的一个概念，也就是锁的持有是以线程为单位而不是基于调用次数。

ReentrantLock提供了很多实用的方法，比如可以控制fairness(公平性)，tryLock()（非阻塞式的获取锁操作），isLocked()，getOwner()。通过调用lock()方法获取，调用unlock()方法释放。


##### synchronized底层实现是什么？lock底层是什么？有什么区别？

|synchronized|Lock|
|---|---|
|关键字|接口/类|
|自动加锁和释放锁|需要手动调用unlock() 方法释放锁|
|JVM层面的锁|API层面的锁（具体实现如 `ReentrantLock`）|
|非公平锁|可以选择公平或者非公平锁|
|锁是一个对象，并且锁的信息保存在了对象中|代码中通过int类型的state标识|
|有一个锁升级的过程|无|



##### 线程生命周期的不同状态？

- 新建（NEW）
- 就绪（RUNNABLE）
- 阻塞（BLOCKED）
- 等待（WAITING），计时等待（TIMED_WAIT）
- 终止（TERMINATED）

##### 性能问题：内存持续上升，我该如何排查问题？

* top：查看CPU 使用率、内存使用率以及系统负载等信息。
* top -Hp pid： 查看具体线程使用系统资源情况
* jstat -gc pid：堆内存信息以及垃圾回收信息
* jstack pid：查看线程的堆栈信息。结合 top -Hp pid 查看具体线程的状态，排查一些死锁的异常
* jmap -dump:live,format=b,file=jmap-dump.phrof [pid]：把堆内存的使用情况 dump 到文件中


使用线程池时 workQueue 过大，任务堆积导致OOM：

- 避免任务堆积。前面我说过newFixedThreadPool是创建指定数目的线程，但是其工作队列是无界的，如果工作线程数目太少，导致处理跟不上入队的速度，这就很有可能占用大量系统内存，甚至是出现OOM。诊断时，你可以使用jmap之类的工具，查看是否有大量的任务对象入队。

##### 什么情况下Java程序会产生死锁？如何定位、修复？

基本上死锁的发生是因为：

- **互斥条件**，类似Java中Monitor都是独占的，要么是我用，要么是你用。
- 互斥条件是**长期持有**的，在使用结束之前，自己不会释放，也不能被其他线程抢占。
- **循环依赖**关系，两个或者多个个体之间出现了锁的链条环。

避免死锁的思路和方法:

* 尽量避免使用多个锁，并且只有需要时才持有锁。
* 如果必须使用多个锁，尽量设计好锁的获取顺序。
* 使用带超时的方法，Object.wait(…)或CountDownLatch.await(…)。
* 通过静态代码分析（如FindBugs）去查找固定的模式，进而定位可能的死锁或者竞争情况。



##### Java并发包提供了哪些并发工具类？

并发包也就是java.util.concurrent及其子包

- 提供了比synchronized更加高级的各种同步结构，包括CountDownLatch、CyclicBarrier、Semaphore等，可以实现更加丰富的多线程操作。
- 各种线程安全的容器，比如最常见的ConcurrentHashMap、有序的ConcurrentSkipListMap，或者通过类似快照机制，实现线程安全的动态数组CopyOnWriteArrayList等。
- 各种并发队列实现，如各种BlockingQueue实现，比较典型的ArrayBlockingQueue、 SynchronousQueue或针对特定场景的PriorityBlockingQueue等。
- 强大的Executor框架，可以创建各种不同类型的线程池，调度任务运行等，绝大部分情况下，不再需要自己从头实现线程池和任务调度器。

CountDownLatch:

- CountDownLatch，允许一个或多个线程等待某些操作完成。
- CountDownLatch的基本操作组合是countDown/await。调用await的线程阻塞等待countDown足够的次数，不管你是在一个线程还是多个线程里countDown，只要次数足够即可。
- CountDownLatch是不可以重置的，所以无法重用；

CopyOnWriteArrayList:

* CopyOnWrite到底是什么意思呢？它的原理是，任何修改操作，如add、set、remove，都会拷贝原数组，修改后替换原来的数组，通过这种防御性的方式，实现另类的线程安全。




##### Java IO 模型

BIO、NIO、NIO 2（AIO）。
BIO：传统的 java.io 包，它基于流模型实现。交互方式是同步、阻塞的方式，在读、写动作完成之前，线程会一直阻塞在那里。
NIO：可以构建多路复用的、同步非阻塞 IO 程序。
NIO 2：异步 IO 操作基于事件和回调机制。
[Java提供了哪些IO方式？ NIO如何实现多路复用？](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/Java%20%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E9%9D%A2%E8%AF%95%E7%B2%BE%E8%AE%B2/11%20%20Java%E6%8F%90%E4%BE%9B%E4%BA%86%E5%93%AA%E4%BA%9BIO%E6%96%B9%E5%BC%8F%EF%BC%9F%20NIO%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E5%A4%9A%E8%B7%AF%E5%A4%8D%E7%94%A8%EF%BC%9F-%E6%9E%81%E5%AE%A2%E6%97%B6%E9%97%B4.md)



##### 通常线程有哪几种使用方式?

有三种使用线程的方法:

- 继承 Thread 类
- 实现 Runnable 接口
- 实现 Callable 接口

##### 多线程的四种实现方式

- 继承Thread,重写run方法,最后创建Thread 的子类对象,调用start()方法开启线程任务
```java
class MyThread extends Thread { 
	@Override public void run() { 
		System.out.println("do something"); 
} } 
// 创建并启动线程 
new MyThread().start();
```
- 实现Runnable接口,重写run方法,创建Runnable 的实现类对象,通过Thread 的构造传递,调用start() 方法开启线程任务
```java
class MyRunnable implements Runnable {
    @Override public void run() {
        System.out.println("do something");
} }
// 创建并启动线程
new Thread(new MyRunnable()).start();
```

- 实现Callable接口,重写call方法,创建Callable的实现类对象,将Callable 的实现类对象,传递到FutureTask的构造方法中,最后将FutureTask传递到Thread 的构造方法中,通过start()方法开启线程任务
```java
class MyCallable implements Callable<Integer> {
    @Override public Integer call() throws Exception {
        System.out.println("do something"); return 100;
} }
// 创建并启动线程
FutureTask<Integer> futureTask = new FutureTask<>(new MyCallable());
new Thread(futureTask).start();
```
- 使用线程池创建
  `new ThreadPoolExecutor(10,20,30,TimeUnit.MINUTES,new LinkedBlockingQueue<>());`


##### 多线程的生命周期

源码中一共定义了6钟状态。
- 新建（_NEW_）：线程对象刚给创建，但未启动（start）
- 可运行（_RUNNABLE_）：线程已被启动，可以被调度或正在被调度。
- 锁阻塞（_BLOCKED_）：当前线程要获取的锁对象正在被其他线程占用，此时该线程处于Blocked状态。
- 等待阻塞（_WAITING_）：当前线程遇到了wait()，join()等方法。
- 限时等待（_TIMED_WAITING_）：当前线程调用了sleep(时间)，wait(时间)，join(时间)等方法。
- 终止（_TERMINATED_）：线程正常结束或异常提前退出。


#### 集合

##### jdk1.7HashMap

数据结构：数组+链表

##### jdk1.8 HashMap

数据结构：数组+链表+（红黑树）


##### JDK8中的HashMap什么时候将链表转化为红黑树？

- 链表长度大于 8。
- 数组长度（容量）大于等于 64。

##### jdk1.7 ConcurrentHashMap

jdk1.7 ConcurrentHashMap底层是由两层嵌套数组来实现的：

1. ConcurrentHashMap对象中有一个属性segments，类型为Segment[];
2. Segment对象中有一个属性table，类型为HashEntry[];

##### jdk1.7 ConcurrentHashMap如何保证并发

主要利用Unsafe操作+ReentrantLock+分段思想。
主要使用了Unsafe操作中的：
- compareAndSwapObject：通过cas的方式修改对象的属性
- putOrderedObject：并发安全的给数组的某个位置赋值
- getObjectVolatile：并发安全的获取数组某个位置的元素

##### jdk1.8 ConcurrentHashMap如何保证并发

主要利用Unsafe操作+synchronized关键字。
JDK8中其实仍然有分段锁的思想，只不过JDK7中段数是可以控制的，而JDK8中是数组的每一个位置都有一把锁。



#### JVM

##### 类加载的五个步骤

1. **加载**：导入 class 文件到 JVM中。
2. **验证**：检查 class 文件的正确性。
3. **准备**：为静态变量分配内存。
4. **解析**：将符号引用转换为直接引用。
5. **初始化**：初始化静态变量和静态代码块。

##### 什么是双亲委派模型

1. **类加载请求向上委托**：类加载器收到加载请求后，先委托给父加载器尝试加载。
2. **层级加载尝试**：
  - **引导类加载器（Bootstrap）**：首先尝试加载，检查 `jre/lib`。
  - **扩展类加载器（Extension）**：若引导类加载器未找到，检查 `jre/lib/ext`。
  - **应用类加载器（Application）**：若扩展类加载器未找到，检查应用的 `classPath`。
3. **未找到类时抛出异常**：如果所有加载器都未找到类，抛出 `ClassNotFoundException`。



##### 谈谈JVM内存区域的划分

1.  **堆**：所有线程共享，存放对象实例。
2. **方法区**：存储类信息、常量、静态变量。
3. **虚拟机栈**：存储局部变量、操作数、方法调用等。
4. **本地方法栈**：为虚拟机调用本地方法服务。
5. **程序计数器**：指示当前线程执行的字节码位置。

![JVM运行时数据区](https://onecup-image.oss-cn-beijing.aliyuncs.com/imgs/typora/202312271832459.webp)





##### JVM哪些区域可能发生OutOfMemoryError

* 堆内存不足，抛出的错误信息是“java.lang.OutOfMemoryError:Java heap space”，原因可能存在内存泄漏问题
* 程序不断的进行递归调用，而且没有退出条件，JVM实际会抛出StackOverFlowError，如果JVM试图去扩展栈空间的的时候失败，则会抛出OutOfMemoryError。

##### 怎么判断对象是否可以被回收？

1. **引用计数法**：检查对象的引用数量，无引用则可回收。
2. **可达性分析**：从GC Roots开始，无法到达的对象可回收。

##### JVM有哪些垃圾回收算法？

1. **标记-清除**（Mark-Sweep）：标记无用对象，然后清除。
2. **复制**（Copying）：将内存分为两块，一块用完后将活动对象复制到另一块。
3. **标记-整理**（Mark-Compact）：标记无用对象，然后移动活动对象压缩空间。
4. **分代收集**（Generational Collection）：根据对象存活时间将内存分代（新生代、老年代），分别回收。


## MySQL

##### MySQL支持的事务隔离级别

MySQL事务隔离级别分为四个不同层次：

- 读未提交（Read uncommitted），就是一个事务能够看到其他事务尚未提交的修改，这是最低的隔离水平，允许[脏读](https://en.wikipedia.org/wiki/Isolation_(database_systems)#Dirty_reads)出现。
- 读已提交（Read committed），事务能够看到的数据都是其他事务已经提交的修改，也就是保证不会看到任何中间性状态，当然脏读也不会出现。读已提交仍然是比较低级别的隔离，并不保证再次读取时能够获取同样的数据，也就是允许其他事务并发修改数据，允许不可重复读和幻象读（Phantom Read）出现。
- 可重复读（Repeatable reads），保证同一个事务中多次读取的数据是一致的，这是MySQL InnoDB引擎的默认隔离级别，但是和一些其他数据库实现不同的是，可以简单认为MySQL在可重复读级别不会出现幻象读。
- 串行化（Serializable），并发事务之间是串行化的，通常意味着读取需要获取共享读锁，更新需要获取排他写锁。



##### MySQL组合索引的使用规则？

mysql (a,b,c) 索引生效 请问哪些索引生效（）

生效的规则是：从前往后依次使用生效，如果中间某个索引没有使用，那么断点前面的索引部分起作用，断点后面的索引没有起作用； 比如:

```
-- 
where a=3 and b=45 and c=5 .... 这种三个索引顺序使用中间没有断点，全部发挥作用；
where a=3 and c=5... 这种情况下b就是断点，a发挥了效果，c没有效果
where b=3 and c=4... 这种情况下a就是断点，在a后面的索引都没有发挥作用，这种写法联合索引没有发挥任何效果；
where b=45 and a=3 and c=5 .... 这个跟第一个一样，全部发挥作用，abc只要用上了就行，跟写的顺序无关

-- 
(6)    select * from mytable where a=3 order by b;
a用到了索引，b在结果排序中也用到了索引的效果，前面说了，a下面任意一段的b是排好序的
(8)    select * from mytable where b=3 order by a;
b没有用到索引，排序中a也没有发挥索引效果
```

##### MySQL索引失效的常见场景？

1. **使用函数或计算**：在索引列上使用函数或算术运算。
2. **不等运算**：使用`!=`或`<>`。
3. **多列OR条件**：在`OR`条件中连接多个列。
5. **LIKE通配符**：`LIKE`查询以通配符开头。
7. **索引列计算/类型转换**：索引列参与计算或类型转换。
8. **使用NULL值**：索引列频繁使用NULL。
9. **复合索引列顺序**：未按索引列顺序查询。
11. **查询大量数据**：返回表中大部分数据。

记忆要点：避免在索引列上做运算、使用不等运算、不恰当的LIKE语句、数据类型不匹配和低选择性索引列。

##### MySQL优化手段

1. **合理使用索引**：
  - 在经常作为查询条件的列上建立索引。
  - 避免在索引列上使用计算和不等运算（如`NOT IN`, `<>`）。
  - 保证索引列的数据类型不被改变。
3. **避免不必要的计算**：
  - 避免在查询中进行复杂的计算，特别是在索引列上。
4. **优化查询语句**：
  - 避免使用`SELECT *`，使用`LIMIT 1`。
  - 使用关联（JOIN）查询代替子查询。
  - 确保`FROM`子句中的表顺序最优，通常是选择记录数最少的表作为驱动表。
  - 对于复杂的查询语句，使用`EXPLAIN`进行性能分析。
5. **管理大表**：
  - 保证单表数据量控制在合理范围内（如不超过200万条），必要时进行表的分割。

##### 项目中使用到的 MySQL 数据类型

1. **整数类型**：`INT`用于普通整数，如用户ID。
2. **精确小数**：`DECIMAL`用于财务计算，如金额。
3. **浮点数**：`FLOAT`、`DOUBLE`用于科学计算。
4. **日期时间**：`DATE`只存日期，`DATETIME`和`TIMESTAMP`存日期和时间。
5. **字符串**：`VARCHAR`用于可变长文本，`TEXT`用于长文本，`CHAR`用于短固定长度文本。
7. **布尔值**：`BOOLEAN`或`TINYINT(1)`用于是/否判断。



## 设计模式

##### 用到过哪些设计模式？

单例模式

```
纯Java项目中（CMDB SDK）：
1. redis消息订阅（MsgSubscriManager extends JedisPubSub），使用单例模式
2. 读取Apollo中配置，使用单例模式
```

适配器模式的应用
```java
public interface PaymentProcessor {
    void processPayment(double amount);
}
// PayPal 的支付服务，这是第三方提供的，我们不能修改：
public class PayPalPaymentService {
    public void makePayment(String paymentDetails) {
        // PayPal 支付处理逻辑
        System.out.println("Payment made with PayPal: " + paymentDetails);
    }
}
public class PayPalAdapter implements PaymentProcessor {
    private PayPalPaymentService payPalService;

    public PayPalAdapter(PayPalPaymentService service) {
        this.payPalService = service;
    }

    @Override public void processPayment(double amount) {
        String paymentDetails = "Amount: " + amount;
        payPalService.makePayment(paymentDetails);
    }
}
// 在应用程序中，我们现在可以使用 `PayPalAdapter` 来处理支付，就像使用任何其他实现了 `PaymentProcessor` 接口的类一样：
public class ECommerceApplication {
    public static void main(String[] args) {
        PaymentProcessor paymentProcessor = new PayPalAdapter(new PayPalPaymentService());
        paymentProcessor.processPayment(100.0);
    }
}
```

策略模式

[设计模式之----匹配器处理器模式（Matcher-Handler）的理解](https://blog.csdn.net/ws9029/article/details/117229616)

```java
策略模式的升级版（matcher-handler）,Matcher-Handler模式也是属于略模式的一种.
isMatcher()
prcoess()
@Autowired
private List<IResMsgHandle> resHandlers;
```

代理模式
在 `ServiceFactory` 类中，通过调用 `Proxy.newProxyInstance` 来创建接口的代理实例。
```java
// 接口实例工厂，这里主要是用于提供接口的实例对象
public class ServiceFactory<T> implements FactoryBean<T> {
    @Override
    public T getObject() throws Exception {
        //这里主要是创建接口对应的实例，便于注入到spring容器中
        InvocationHandler handler = new ServiceProxy<>(interfaceType);
        return (T) Proxy.newProxyInstance(interfaceType.getClassLoader(),
                new Class[]{interfaceType}, handler);
```

实现 `BeanDefinitionRegistryPostProcessor` 接口，自定义Bean的注册过程。扫描包含特定注解或符合条件的接口.
```java
// 用于Spring动态注入自定义接口
public class ServiceBeanDefinitionRegistry implements BeanDefinitionRegistryPostProcessor, ResourceLoaderAware, ApplicationContextAware {
      @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry)
            //这里一般我们是通过反射获取需要代理的接口的clazz列表
        //比如判断包下面的类，或者通过某注解标注的类等等
        Set<Class<?>> beanClazzs = scannerPackages("com.ultra.cmdb.dao");
				definition.setBeanClass(ServiceFactory.class);
```
- 实现 `InvocationHandler` 接口，重写 `invoke` 方法以添加自定义逻辑。可在 `invoke` 方法中添加日志记录、性能监控、事务处理等。
```java
// 动态代理，需要注意的是，这里用到的是JDK自带的动态代理，代理对象只能是接口，不能是类
public class ServiceProxy<T> implements InvocationHandler {
    @Override
    public Object invoke(Object proxy, Method method, Object[] args)
        Class<?> returnType = method.getReturnType();
        Query annotation = method.getAnnotation(Query.class);
```

记忆点

- **工厂创建代理**：使用 `ServiceFactory` 动态创建接口代理。
- **Spring集成**：通过 `ServiceBeanDefinitionRegistry` 在Spring中注册代理。
- **自定义行为**：`ServiceProxy` 用于添加特定业务逻辑。



## 数据结构与算法


##### 冒泡排序

每次遍历时都对相邻两个元素排序


## 组件


#### Redis

##### 项目中缓存是如何使用的？为什么要用缓存？缓存使用不当会造成什么后果？
###### 为什么要用缓存？

用缓存，主要有两个用途：**高性能**（查询速度快）、**高并发**（QPS高）。

##### Redis 和 Memcached 有什么区别？Redis 的线程模型是什么？为什么 Redis 单线程却能支撑高并发？

##### Redis 和 Memcached 有啥区别？

Redis 支持复杂的数据结构
Redis 原生支持集群模式
Redis 只使用单核，小数据量时性能更高

##### 为啥 Redis 单线程模型也能效率这么高？

纯内存操作。
**核心是**基于非阻塞的 IO 多路复用机制。
C 语言实现。
单线程反而避免了多线程的频繁上下文切换问题，预防了多线程可能产生的竞争问题。


##### Redis 都有哪些数据类型？分别在哪些场景下使用比较合适？

Redis 主要有以下几种数据类型：

- Strings
- Hashes
- Lists
- Sets
- Sorted Sets

> Redis 除了这 5 种数据类型之外，还有 Bitmaps、HyperLogLogs、Streams 等。

项目中实际使用到哪些数据结构？

* string

  ```java
  // 对String操作的命令: set(key, value)：给数据库中名称为key的string赋予值value
  jedisCluster.set(key, value);
  ```


##### Redis 的过期策略都有哪些？内存淘汰机制都有哪些？手写一下 LRU 代码实现？

Redis 过期策略是：定期删除+惰性删除。

Redis 内存块耗尽了，走内存淘汰机制。

Redis 内存淘汰机制有以下几个：
- **allkeys-lru**：当内存不足以容纳新写入数据时，在**键空间**中，移除最近最少使用的 key（这个是**最常用**的）。
- volatile-lru：当内存不足以容纳新写入数据时，在**设置了过期时间的键空间**中，移除最近最少使用的 key（这个一般不太合适）。

##### 手写一个 LRU 算法

```java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private int capacity;
    /**
     * 传递进来最多能缓存多少数据
     *
     * @param capacity 缓存大小
     */
    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }

    /**
     * 如果map中的数据量大于设定的最大容量，返回true，再新加入对象时删除最老的数据
     *
     * @param eldest 最老的数据项
     * @return true则移除最老的数据
     */
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        // 当 map中的数据量大于指定的缓存个数的时候，自动移除最老的数据
        return size() > capacity;
    }
}
```


##### Redis 持久化的两种方式

RDB：RDB 持久化机制，是对 Redis 中的数据执行周期性的持久化。
AOF：AOF 机制对每条写入命令作为日志，以 append-only 的模式写入一个日志文件中，在 Redis 重启的时候，可以通过回放 AOF 日志中的写入指令来重新构建整个数据集。

###### RDB 优缺点

- RDB 会生成数据文件，每个数据文件都代表了某一个时刻中 Redis 的数据，**非常适合做冷备**，
- RDB 对 Redis 对外提供的读写服务，影响非常小。
- 直接基于 RDB 数据文件来重启和恢复 Redis 进程，更加快速。
- RDB 数据快照文件，都是每隔 5 分钟，或者更长时间生成一次，这个时候就得接受一旦 Redis 进程宕机，那么会丢失最近 5 分钟（甚至更长时间）的数据。

###### AOF 优缺点

- 一般 AOF 会每隔 1 秒，通过一个后台线程执行一次 `fsync` 操作，最多丢失 1 秒钟的数据。
- AOF 日志文件以 `append-only` 模式写入，写入性能非常高。
- 对于同一份数据来说，AOF 日志文件通常比 RDB 数据快照文件更大。


##### Redis 集群模式的工作原理能说一下么？在集群模式下，Redis 的 key 是如何寻址的？分布式寻址都有哪些算法？了解一致性 hash 算法吗？

###### Redis cluster 的 hash slot 算法

Redis cluster 有固定的 16384 个 hash slot，对每个 key 计算 CRC16 值，然后对 16384 取模，可以获取 key 对应的 hash slot。

Redis cluster 中每个 master 都会持有部分 slot。客户端的 api，可以对指定的数据，让他们走同一个 hash slot，通过 hash tag 来实现。

任何一台机器宕机，另外两个节点，不影响的。因为 key 找的是 hash slot，不是机器。


###### Redis cluster 的高可用与主备切换原理

Redis cluster 的高可用的原理，几乎跟哨兵是类似的。

判断节点宕机

如果一个节点认为另外一个节点宕机，那么就是 pfail ，主观宕机。如果多个节点都认为另外一个节点宕机了，那么就是 fail ，客观宕机，跟哨兵的原理几乎一样，sdown，odown。

在 cluster-node-timeout 内，某个节点一直没有返回 pong ，那么就被认为 pfail 。

如果一个节点认为某个节点 pfail 了，那么会在 gossip ping 消息中， ping 给其他节点，如果超过半数的节点都认为 pfail 了，那么就会变成 fail 。

##### 了解什么是 Redis 的雪崩、穿透和击穿？Redis 崩溃之后会怎么样？系统该如何应对这种情况？如何处理 Redis 的穿透？

###### 缓存雪崩

缓存机器意外发生了全盘宕机。缓存挂了，此时 1 秒 5000 个请求全部落数据库，数据库必然扛不住。

解决方案如下：

- 事前：Redis 高可用，Redis cluster，避免全盘崩溃。
- 事中：本地 ehcache 缓存 + hystrix 限流&降级，避免 MySQL 被打死。
- 事后：Redis 持久化，一旦重启，自动从磁盘上加载数据，快速恢复缓存数据。

###### 缓存穿透

恶意攻击查询不存在的数据，缓存中查不到，每次你去数据库里查，缓存穿透就会直接把数据库给打死。

解决方案如下：

- 每次系统 A 从数据库中只要没查到，就写一个空值到缓存里去，比如 `set -999 UNKNOWN` 。

- 使用布隆过滤器。请求数据的 key 不存在于布隆过滤器中，可以确定数据就一定不会存在于数据库中，系统可以立即返回不存在。请求数据的 key 存在于布隆过滤器中，则继续再向缓存中查询。

###### 缓存击穿

就是说某个 key 非常热点，访问非常频繁，当这个 key 在失效的瞬间，大量的请求就击穿了缓存，直接请求数据库。

不同场景下的解决方式可如下：

- 若缓存的数据是基本不会发生更新的，则可尝试将该热点数据设置为永不过期。
- 若缓存的数据更新不频繁，则可以采用基于 Redis 分布式锁，或者本地互斥锁以保证仅少量的请求能请求数据库并重新构建缓存，其余线程则在锁释放后能访问到新缓存。
- 若缓存的数据更新频繁，可以利用定时线程在缓存过期前主动地重新构建缓存或者延后缓存的过期时间。


##### 如何保证缓存与数据库的双写一致性？

###### 最初级的缓存不一致问题及解决方案

问题：先更新数据库，再删除缓存。如果删除缓存失败了，那么会导致数据库中是新数据，缓存中是旧数据，数据就出现了不一致。

解决思路：延时双删。依旧是先更新数据库，再删除缓存，唯一不同的是，我们把这个删除的动作，在不久之后再执行一次，比如 5s 之后。

删除的动作，可以选择放在 `MQ` 中。

##### Redis作为消息队列使用

* 消息队列

  ```java
  // 发送Redis消息
  sendMsg(data, objType, opType);
    jedis.publish(preFix + ChannelName.FROM_CMDB.name(), opObj.toString());
  
  // 订阅消息
  public class MsgSubscriber extends JedisPubSub implements ApplicationRunner{
            // 订阅信道
            jedis.subscribe(sub, channelName);
   
    @Override
    public void onMessage(String channel, String message) {
        hdlMng.handleMsg(message);
    }
  ```


##### Redis与MySQL双写一致性如何保证？
* 延时双删策略
  * 在写库前后都进行redis.del(key)操作，并且设定合理的超时时间。具体步骤是：
  * 1）先删除缓存
  * 2）再写数据库
  * 3）休眠500毫秒（根据具体的业务时间来定）
  * 4）再次删除缓存。
* 设置缓存的过期时间
* 如何写完数据库后，再次删除缓存成功？
  * （1）更新数据库数据；
    （2）数据库会将操作信息写入binlog日志当中；
    （3）订阅程序提取出所需要的数据以及key；
    （4）另起一段非业务代码，获得该信息；
    （5）尝试删除缓存操作，发现删除失败；
    （6）将这些信息发送至消息队列；
    （7）重新从消息队列中获得该数据，重试操作。



##### Redis实现分布式锁？

参考：[基于Redis的分布式锁实现](https://juejin.cn/post/6844903830442737671)

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface RedisLock {
    // 业务键
    String key();
    // 锁的过期秒数,默认是5秒
    int expire() default 5;
    // 尝试加锁，最多等待时间
    long waitTime() default Long.MIN_VALUE;
    // 锁的超时时间单位
    TimeUnit timeUnit() default TimeUnit.SECONDS;
}


/** 在AOP中我们去执行获取分布式锁和释放分布式锁的逻辑 **/
@Aspect
@Component
public class LockMethodAspect {
    @Around("@annotation(com.redis.lock.annotation.RedisLock)")
    public Object around(ProceedingJoinPoint joinPoint) {
        Method method = signature.getMethod();
        RedisLock redisLock = method.getAnnotation(RedisLock.class);
        String value = UUID.randomUUID().toString();
        String key = redisLock.key();
        try {
            final boolean islock = redisLockHelper.lock(jedis,key, value, redisLock.expire(), redisLock.timeUnit());
          return joinPoint.proceed();
        }  finally {
          // 释放锁
            redisLockHelper.unlock(jedis,key, value);
            jedis.close();
        }
    }
}


/** Redis实现分布式锁核心类 **/
@Component
public class RedisLockHelper {
    // 在Redis的2.6.12及以后中,使用 set key value [NX] [EX] 命令
    public boolean lock(Jedis jedis,String key, String value, int timeout, TimeUnit timeUnit) {
        long seconds = timeUnit.toSeconds(timeout);
        return "OK".equals(jedis.set(key, value, "NX", "EX", seconds));
    }

    // 使用Lua脚本进行解锁操纵，解锁的时候验证value值
    public boolean unlock(Jedis jedis,String key,String value) {
        String luaScript = "if redis.call('get',KEYS[1]) == ARGV[1] then " +
                "return redis.call('del',KEYS[1]) else return 0 end";
        return jedis.eval(luaScript, Collections.singletonList(key), Collections.singletonList(value)).equals(1L);
    }
}


/** 定义一个TestController来测试我们实现的分布式锁 **/
public class TestController {
    @RedisLock(key = "redis_lock")
    @GetMapping("/index")
    public String index() {

```



#### Eureka

##### Eureka的CAP特性，服务宕机了怎么办？

**CAP 定理**：

- C：数据一致性；
- A：服务可用性；
- P：服务对网络分区故障的容错性

**Eureka**：

- 属性：AP系统，保证可用性。
- 特点：所有节点平等，数据相同，可相互交叉注册。使用轮询负载均衡，但可能不具备一致性特性。


Eureka服务宕机了怎么办：

* Eureka Server可以运行多个实例来构建集群，解决单点问题。如果某台Eureka Server宕机，Eureka Client的请求会自动切换到新的Eureka Server节点，当宕机的服务器重新恢复后，Eureka会再次将其纳入到服务器集群管理之中。

参考：[2021升级版微服务教程3—Eureka完全使用指南](https://www.cnblogs.com/bingyang-py/p/14255298.html)


##### Eureka 和 Nacos 有什么区别？

1. **用途**：

  - **Eureka**：服务注册与发现。
  - **Nacos**：服务注册与发现、动态配置、健康监测。
3. **数据存储**：

  - **Eureka**：内存。（使用基于内存的数据存储，所有的服务实例信息都存储在内存中。）
  - **Nacos**：支持多种持久化存储。（支持多种持久化存储，包括 MySQL、Redis 等。）
7. **CAP模型**：

  - **Eureka**：AP 模型。
  - **Nacos**：支持 AP 或 CP 模型，可以根据具体需求进行配置。



#### Kafka

##### 为什么使用消息队列

**异步**、**削峰**、**解耦**。

##### 如何保证消息队列的高可用？

Kafka 由多个 broker 组成。创建的 topic 划分为多个 partition，每个 partition 可以存在于不同的 broker 上。

Kafka 提供了 HA 机制，就是 replica 副本机制。每个 partition 的数据都会同步到其它机器上，形成自己的多个 replica 副本。所有 replica 会选举一个 leader 出来，那么生产和消费都跟这个 leader 打交道。写的时候，leader 会负责把数据同步到所有 follower 上去，读的时候就直接读 leader 上的数据即可。如果某个 broker 宕机了，那么此时会从 follower 中**重新选举**一个新的 leader 出来。

##### 如何保证消息不被重复消费？

或者说，如何保证消息消费的幂等性？
- 比如你拿个数据要写库，你先根据主键查一下，如果这数据都有了，你就别插入了，update 一下好吧。
- 比如你是写 Redis，那没问题了，反正每次都是 set，天然幂等性。
- 比如你不是上面两个场景，那做的稍微复杂一点，你需要让生产者发送每条数据的时候，里面加一个全局唯一的 id，类似订单 id 之类的东西，然后你这里消费到了之后，先根据这个 id 去比如 Redis 里查一下，之前消费过吗？如果没有消费过，你就处理，然后这个 id 写 Redis。如果消费过了，那你就别处理了，保证别重复处理相同的消息即可。
- 比如基于数据库的唯一键来保证重复数据不会重复插入多条。因为有唯一键约束了，重复数据插入只会报错，不会导致数据库中出现脏数据。

##### 如何处理消息丢失的问题？

或者说，如何保证消息的可靠性传输？

###### 消费端弄丢了数据
**关闭自动提交** offset，在处理完之后自己手动提交 offset，就可以保证数据不会丢。

###### Kafka 弄丢了数据
Kafka 某个 broker 宕机，然后重新选举 partition 的 leader，follower 刚好还有些数据没有同步。

设置如下 4 个参数：
- 给 topic 设置 `replication.factor` 参数：这个值必须大于 1，要求每个 partition 必须有至少 2 个副本。
- 在 Kafka 服务端设置 `min.insync.replicas` 参数：这个值必须大于 1，这个是要求一个 leader 至少感知到有至少一个 follower 还跟自己保持联系，没掉队，这样才能确保 leader 挂了还有一个 follower 吧。
- 在 producer 端设置 `acks=all` ：这个是要求每条数据，必须是**写入所有 replica 之后，才能认为是写成功了**。
- 在 producer 端设置 `retries=MAX` （很大很大很大的一个值，无限次重试的意思）：这个是**要求一旦写入失败，就无限重试**，卡在这里了。

###### 生产者会不会弄丢数据？
设置了 `acks=all` ，一定不会丢，要求是，你的 leader 接收到消息，所有的 follower 都同步到了消息之后，才认为本次写成功了。如果没满足这个条件，生产者会自动不断的重试，重试无限次。

##### 如何保证消息的顺序性？

解决方案
- 一个 topic，一个 partition，一个 consumer，内部单线程消费。
- 生产者在写的时候，可以指定一个 key，比如说指定某个订单 id 作为 key，这个订单相关的数据，一定会被分发到同一个 partition 中去，而且这个 partition 中的数据一定是有顺序的。

- 消费者里可能会搞**多个线程来并发处理消息**导致的顺序错乱。写 N 个内存 queue，具有相同 key 的数据都到同一个内存 queue；然后对于 N 个线程，每个线程分别消费一个内存 queue 即可，这样就能保证顺序性。

##### 消息持续积压怎么解决？

如何解决消息队列的延时以及过期失效问题？消息队列满了以后该怎么处理？有几百万消息持续积压几小时，说说怎么解决？

###### 大量消息在 mq 里积压了几个小时了还没解决

几千万条数据在 MQ 里积压了七八个小时，从下午 4 点多，积压到了晚上 11 点多。

一般这个时候，只能临时紧急扩容了，具体操作步骤和思路如下：

- 先修复 consumer 的问题，确保其恢复消费速度，然后将现有 consumer 都停掉。
- 新建一个 topic，partition 是原来的 10 倍，临时建立好原先 10 倍的 queue 数量。
- 然后写一个临时的分发数据的 consumer 程序，这个程序部署上去消费积压的数据，**消费之后不做耗时的处理**，直接均匀轮询写入临时建立好的 10 倍数量的 queue。
- 接着临时征用 10 倍的机器来部署 consumer，每一批 consumer 消费一个临时 queue 的数据。这种做法相当于是临时将 queue 资源和 consumer 资源扩大 10 倍，以正常的 10 倍速度来消费数据。
- 等快速消费完积压数据之后，**得恢复原先部署的架构**，**重新**用原先的 consumer 机器来消费消息。

###### mq 中的消息过期失效了

我们可以采取一个方案，就是**批量重导**。就是大量积压的时候，我们当时就直接丢弃数据了，然后等过了高峰期以后，这个时候我们就开始写程序，将丢失的那批数据，写个临时程序，一点一点的查出来，然后重新灌入 mq 里面去，把白天丢的数据给他补回来。

###### mq 都快写满了

临时写程序，接入数据来消费，**消费一个丢弃一个，都不要了**，快速消费掉所有的消息。然后走第二个方案，到了晚上再补数据吧。


对于 RocketMQ，官方针对消息积压问题，提供了解决方案。

1. 提高消费并行度
2. 批量方式消费
3. 跳过非重要消息
4. 优化每条消息消费过程

#### ElasticSearch

##### ES常用关键字 match match_all，ES内置分词器？

关键字

* `match_all`表示查询所有的数据

* 在字段中搜索特定字词，可以使用`match`; 如下语句将查询address 字段中包含 mill 或者 lane的数据

  ```bash
  GET /bank/_search
  {
    "query": { "match": { "address": "mill lane" } }
  }
  ```

* 查询段落匹配：match_phrase

  如果我们希望查询的条件是 address字段中包含 “mill lane”，则可以使用`match_phrase`

  ```bash
  match_phrase还是分词后去搜的，目标文档需要包含分词后的所有词，目标文档还要保持这些词的相对顺序和文档中的一致
  
  GET /bank/_search
  {
    "query": { "match_phrase": { "address": "mill lane" } }
  }
  ```

* 使用`bool`查询来组合多个查询条件。

* `must`, `should`, `must_not` 和 `filter` 都是`bool`查询的子句。那么`filter`和上述`query`子句有啥区别呢？

* 查询条件：query or filter，query 上下文的条件是用来给文档打分的，匹配越好 _score 越高；filter 的条件只产生两种结果：符合与不符合，后者被过滤掉。

* 聚合查询：Aggregation。aggs，avg

* 模糊匹配：fuzzy

* Term查询：term是代表完全匹配，也就是精确查询，搜索前不会再对搜索词进行分词拆解。

  ```json
  // 查询不到  "title": "love China",
  {
    "query": {
      "term": {
        "title": "love China"
      }
    }
  }
  // 执行发现无数据，从概念上看，term属于精确匹配，只能查单个词
  ```

完整语句参考

* ```json
  "query": {
    "bool": {
      "must": [
        "match": {
            "state": "ND"
        "filter": [
          "term": {
            "age": "40"
        "range": {
          "balance": {
          "gte": 20000, "lte": 30000
  ```



Elasticsearch的内置分词器

- Standard Analyzer - 默认分词器，按词切分，小写处理
- Simple Analyzer - 按照非字母切分(符号被过滤), 小写处理
- Whitespace Analyzer - 按照空格切分，不转小写
- Keyword Analyzer - 不分词，直接将输入当作输出
- Patter Analyzer - 正则表达式，默认\W+(非字符分割)
- Customer Analyzer 自定义分词器

##### 实际项目中如何使用 IK 分词器？

1. **安装IK分词器**：
  - 将IK分词器作为插件安装到Elasticsearch服务中。
2. **配置Elasticsearch索引**：
  - 在创建索引时指定字段使用IK分词器进行分词。可以选择`ik_smart`（精确模式）或`ik_max_word`（全面模式）。
```json
PUT /my_index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "ik_analyzer": { 
          "type": "custom",
          "tokenizer": "ik_max_word"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "content": { 
        "type": "text",
        "analyzer": "ik_analyzer"
      }
    }
  }
}

```

3. **在应用中使用**：

##### IK分词器分词模式

**ik_smart（精确模式）**：使用`ik_smart`分词可能得到：`["中华人民共和国", "万岁"]`
**ik_max_word（全面模式）**：使用`ik_max_word`分词可能得到：`["中华", "华人", "人民", "共和", "共和国", "中华人民共和国", "万岁"]`





