# redis
!> **redis** 面试题以及答案整理

**重点：看书<<redis设计与实现>>**


> redis就数据结构 内存淘汰机制 单线程 高可用 缓存应用


## redis的数据结构
> 因为小公司项目 Redis 用的是 String 数据结构。
> 面试官其实想考察的是 Redis 的数据结构，这个时候就应该主动告诉面试官自己知道 Redis 的数据结构！
> 类似问题：redis的数据类型

1. 5种基本数据结构：string，list，hash，set，zset
2. 3种高级数据结构：bitmap、geo、hyperloglog
3. redis5.0引入的数据结构 streams，这是Redis5.0引入的全新数据结构，用一句话概括Streams就是Redis实现的内存版kafka。而且，Streams也有Consumer Groups的概念。通过Redis源码中对stream的定义我们可知，streams底层的数据结构是radix tree：

[redis中的hashtable介绍](https://blog.csdn.net/u010710458/article/details/80604740)

1. [参考](https://blog.csdn.net/assasin0308/article/details/103965255)
2. [参考](https://juejin.cn/post/6844903644798664712)


类似问题：
1. redis底层数据结构 2次
2. redis源码

## redis内存淘汰机制

eviction是驱逐的意思。volatile
1. noeviction：当内存使用超过配置的时候会返回错误，不会驱逐任何键。
2. allkeys-lru：加入键的时候，如果过限，首先通过LRU算法驱逐最久没有使用的键
3. volatile-lru：加入键的时候如果过限，首先从设置了过期时间的键集合中驱逐最久没有使用的键
4. allkeys-random：加入键的时候如果过限，从所有key随机删除
5. volatile-random：加入键的时候如果过限，从过期键的集合中随机驱逐
6. volatile-ttl：从配置了过期时间的键中驱逐马上就要过期的键
7. volatile-lfu：从所有配置了过期时间的键中驱逐使用频率最少的键
8. allkeys-lfu：从所有键中驱逐使用频率最少的键


类似问题：redis数据淘汰机制
> 为什么手动淘汰 Redis 中的数据，不使用 Redis 的内存淘汰机制？
> 因为 Redis 的内存淘汰机制是对 Key 值进行筛选，而红包池的机制是对 Value 值的筛选。面试官提及了内存淘汰机制，应该主动告诉面试官自己知道 Redis 的内存淘汰机制！

1. noeviction: 当内存不足以容纳新写入数据时，新写入操作会报错，这个一般没人用吧，实在是太恶心了
2. allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 key（这个是最常用的）
3. allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个 key，这个一般没人用吧，为啥要随机，肯定是把最近最少使用的 key 给干掉啊。
4. volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的 key（这个一般不太合适）
5. volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个 key
6. volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的 key 优先移除

redis4.0 引入了volatile-lfu和allkeys-lfu淘汰策略，将访问频率最少的键值对淘汰


![znfHJ6](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/znfHJ6.png)

## 同时对Redis和数据库进行内存更新的时候，如何保证操作的成功 / Redis更新后线程挂掉了怎么办？
1. [参考](https://blog.csdn.net/qq32933432/article/details/108690254)
2. [参考](https://www.cnblogs.com/mr-ziyoung/p/14048326.html)
3. 自己还是再好好查一下

## 有序集合是什么 
!> 我说了排序，然后问怎么排序的 然后又问有序集合zset的时间复杂度

## redis是单线程模型还是多线程模型
!> 进阶问题：redis单线程模型为什么可以那么快（说了非阻塞IO的实现selector，定时任务的实现）
!> 项目里面用redis来干什么？说了一下购物车的实现

## zset实现原理
1. [参考](https://www.jianshu.com/p/360627bd04e5)
2. [【推荐】参考](https://www.cnblogs.com/aspirant/p/11475295.html)

## zset如何实现有序

![dSnA7L](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/dSnA7L.png)
图来自于<<redis设计与实现>>8.6

## zset的时间复杂度

## redis 持久化

## redis 跳表 
## redis hashtable实现，什么时候退化成ziplist
## ziplist 和 hashtable 之间区别

## redis 持久化有哪几种，主要用来存什么数据，redis 崩了怎么办（持久化没答上来， 说这是运维的工作 ，崩了说的哨兵，顺带说了下集群）

## 问了上次面试的问题，redis 怎么做持久化，这次答出来了，因为听说字节复盘很重要

## 缓存雪崩怎么解决
正常的缓存流程：![sTLgm3](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/sTLgm3.png)
缓存雪崩描述：大量的redis缓存在同一时间内过期，导致请求直接打到数据库上造成数据库崩溃
[参考](https://www.bilibili.com/video/BV1f5411b7ux?from=search&seid=16353605540017451556)


解决方案：
1. 设置缓存的失效时间，让缓存不要同一时间失效，我们设置缓存的时候可以随机初始化它的失效时间
2. redis一般都是集群部署，我们把热点的key放到不同的节点上去，让这些热点的缓存，平均分到不同节点上
3. 跑定时任务，定时去刷新缓存，比如缓存快过期的时候用定时任务重新刷新缓存

缓存穿透：指的是缓存和数据库都没有的数据，一般常见于黑客攻击，比如用请求id=-1的数据，这种数据直接穿透缓存，打到数据库上，导致数据库挂掉。![EWfzUc](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/EWfzUc.png)

解决方案：
1. 将数据库查出的结果保存到redis缓存中
2. 直接将其IP拉黑
3. 对参数进行合法性校验，对于不合法参数直接过滤掉
4. 布隆过滤器，会单独拿出视频来讲

缓存击穿：某一个非常热点的key失效，一瞬间大量该key的请求打到数据库上，造成数据库挂掉。![HENL1g](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/HENL1g.png)

解决方案：
1. 设置缓存不过期
2. 【最好的方法】设置分布式锁，如果是单体应用，设置互斥锁

分布式锁原理：首先大量的用户访问redis请求数据，如果有的话，就会返回给用户，如果redis为空的话，就会去数据库请求数据，我们就在这个数据库请求这一步上锁，那么这个时候只有一个线程能抢到这个锁，所以也就只有一个线程能操作这个数据库，这时候对数据库压力比较小，当查询到数据之后直接将数据缓存到redis里面，其他没有抢到锁的线程让它先睡几毫秒，然后再去redis里面查询，因为我们前面有一个线程抢到了锁并将数据缓存到redis里面，所以其他线程访问redis的时候直接可以获取到。

分布式锁实现方式比较多，比如zookeepeer，还有redis实现，会单独拿出视频来讲


思考题：一个项目可以分为如下3个阶段，
1. 上线前的准备：比如是否可以搭成集群，比如mysql集群，redis集群，项目本身的分布式集群，这样的话就形成了一个高可用的集群，增强了系统的健壮性，项目运行的过程中发现了这些问题我们是否可以采用限流降级这些处理措施，防止大量请求打到数据库上，造成系统不可用
2. 项目运行中的准备：
3. 项目宕机之后的处理措施：是否可以集成报警系统，通知我们的开发人员，同时利用redis的rdb以及aof持久化机制快速恢复redis的数据，最大限度减少系统不可用时间。




## 怎么做限流


## 如何保证多个服务器的数据一致性

## 写时修改

## redis数据结构

## redis线程模型


