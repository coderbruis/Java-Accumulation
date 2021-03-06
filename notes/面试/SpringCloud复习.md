## 1. 什么是SpringCloud？

SpringCloud是一系列框架的集合，它通过SpringBoot开发的便利性巧妙地简化了分布式系统的搭建、开发，如服务发现与注册、配置中心、负载均衡、断路器以及数据监控等都可以通过SpringBoot来一键启动和部署。

## 2. SpringCloud的核心组件有哪些？

- Eureka：服务注册于发现
- Feign：基于动态代理机制，根据注解和选择的机器，拼接请求url地址，发起请求
- Ribbon：实现负载均衡
- Hystrix：提供线程池，让不同的服务走不同的线程，实现不同服务间调用的隔离，避免了服务雪崩的问题
- Zuul：网关管理，由Zuul网关转发请求给对应的服务

## 3. SpringCloud和Dubbo

SpringCloud和Dubbo都是现在主流的微服务架构

### 3.1 注册中心的区别

首先，SpringCloud注册中心是Eureka，主要维护的是AP，即可用性和分区容错性；而Dubbo维护的是CP，即一致性和分区容错性；在一个分布式系统中，有一个CAP定理，即C（一致性，Consistency）、A（可用性，Availability）、P（分区容错性，Partition tolerance），但是这三者只能同时实现两点，不可能三者兼顾。而在分布式系统中，P是必须保证的，所以对于一个分布式系统应用来说，要么就是CP、要么就是AP。

> 分区容错性

分区容错性约束了一个分布式系统需要具有如下特性：分布式系统在遇到任何网络分区故障的时候，仍然需要能够保证对外提供满足一致性和可用性的服务，除非是整个网络环境都发生了故障。

> 一致性

在分布式环境中，一致性是指数据在多个副本之间是否能够保持一致的特性。在一直性的需求下，当一个系统在数据一致的状态下执行更新操作后，应该保证系统的数据仍然处于一致的状态。

> 可用性

可用性是指系统提供的服务必须一直处于可用的状态，对于用户的每一个操作请求总是能够在有限的时间内返回结果。

> eureka

在SpringCloud中，使用eureka来作为注册中心。eureka分为了eureka-server和eureka-client。

```
每一个微服务中都有eureka client，用于服务的注册于发现
（服务的注册：把自己注册到eureka server）
（服务的发现：从eureka server获取自己需要的服务列表）

每一个微服务启动的时候，都需要去eureka server注册

当A服务需要调用B服务时，需要从eureka服务端获取B服务的服务列表，然后把列表缓存到本地，然后根据ribbon的客户端负载均衡规则，从服务列表中取到一个B服务，然后去调用此B服务
当A服务下次再此调用B服务时，如果发现本地已经存储了B的服务列表，就不需要再从eureka服务端获取B服务列表，直接根据ribbon的客户端负载均衡规则，从服务列表中取到一个B服务，然后去调用B服务

微服务，默认每30秒，就会从eureka服务端获取一次最新的服务列表

如果某台微服务down机，或者添加了几台机器，
此时eureka server会通知订阅他的客户端，并让客户端更新服务列表，
而且还会通知其他eureka server更新此信息

心跳检测，微服务每30秒向eureka server发送心跳，
eureka server若90s之内都没有收到某个客户端的心跳，则认为此服务出了问题，
会从注册的服务列表中将其删除，并通知订阅它的客户端更新服务列表，
而且还会通知其他eureka server更新此信息
```

> zookeeper

```
zookeeper也可以作为注册中心，用于服务治理（zookeeper还有其他用途，例如：分布式事务锁等）	
每启动一个微服务，就会去zk中注册一个临时子节点，
例如：5台订单服务，4台商品服务
（5台订单服务在zk中的订单目录下创建的5个临时节点）
（4台商品服务在zk中的商品目录下创建的4个临时接点）

每当有一个服务down机，由于是临时接点，此节点会立即被删除，并通知订阅该服务的微服务更新服务列表
（zk上有watch，每当有节点更新，都会通知订阅该服务的微服务更新服务列表）

每当有一个新的微服务注册进来，就会在对应的目录下创建临时子节点，并通知订阅该服务的微服务更新服务列表
（zk上有watch，每当有节点更新，都会通知订阅该服务的微服务更新服务列表）

每个微服务30s向zk获取新的服务列表
```

> zookeeper和eureka的区别

eureka基于AP

zookeeper基于CP

由于作为注册中心可用性的需求要高于一致性，所以eureka貌似要比zookeeper更合理一些


1. ZooKeeper保证的是CP,Eureka保证的是AP

    ZooKeeper在选举期间注册服务瘫痪,虽然服务最终会恢复,但是选举期间不可用的
    Eureka各个节点是平等关系,只要有一台Eureka就可以保证服务可用,而查询到的数据并不是最新的
    自我保护机制会导致Eureka不再从注册列表移除因长时间没收到心跳而应该过期的服务
    Eureka仍然能够接受新服务的注册和查询请求,但是不会被同步到其他节点(高可用)
    当网络稳定时,当前实例新的注册信息会被同步到其他节点中(最终一致性)
    Eureka可以很好的应对因网络故障导致部分节点失去联系的情况,而不会像ZooKeeper一样使得整个注册系统瘫痪

2. ZooKeeper有Leader和Follower角色,Eureka各个节点平等

3. ZooKeeper采用过半数存活原则,Eureka采用自我保护机制解决分区问题

4. Eureka本质上是一个工程,而ZooKeeper只是一个进程



## 4. Dubbo的RPC和SpringCloud的REST


## 5. SpringCloud相比于Dubbo有哪些优势？有哪些缺点？二者的优缺点？

> SpringCloud的优点

- 每一个服务足够内聚,代码容易理解
- 开发效率提高,一个服务只做一件事
- 微服务能够被小团队单独开发
- 微服务是松耦合的,是有功能意义的服务
- 可以用不同的语言开发,面向接口编程
- 易于与第三方集成
- 微服务只是业务逻辑的代码,不会和HTML,CSS或者其他界面组合

        开发中,两种开发模式
                 前后端分离
                 全栈工程师

- 可以灵活搭配,连接公共库/连接独立库

> SpringCloud的缺点

- 分布式系统的复杂性
- 多服务运维难度,随着服务的增加,运维的压力也在增大
- 系统部署依赖
- 服务间通信成本
- 数据一致性
- 系统集成测试
- 性能监控

## 6. 
