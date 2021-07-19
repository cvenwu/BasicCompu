# redis
!> **redis** 面试题以及答案整理

**重点：看书<<redis设计与实现>>**

> redis就数据结构 内存淘汰机制 单线程 高可用 缓存应用

## redis的IO模型
redis采用的是IO多路复用模型，核心分为四个组件：
1. 多路复用程序
2. 套接字队列
3. 事件分派器
4. 时间处理器

但是其实还有一个组件叫做套接字队列：![C94uy3](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/C94uy3.png)

具体流程图：![sFUXVP](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/sFUXVP.png)

所谓的建立连接以及套接字之类的，表现形式就是文件描述符，IO多路复用会挑出准备好(数据已经发过来或者我准备写数据了)的文件描述符，丢过去给套接字队列，事件分发器会从套接字队列里面拿到我们的套接字，之后分发器会挑具体的事件处理器，这个图看懂了，那么redis的IO多路复用就可以答出来了。

建议采用一个实例去记，到时候也好解释
![0cuVTu](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/0cuVTu.png)

**扩展点一：从redis 6.0的多线程模型去讲解**
1. redis这种模型的瓶颈在于从套接字中读写数据，因此在6.0中引入了异步IO线程，专门负责读取IO数据
2. 具体解释：主线程监听到套接字事件，然后找到一个IO线程去读取数据，然后主线程根据命令找到对应的事件处理器，并执行命令，之后写入数据的时候又会找到一个IO线程去写数据。总结就是，读取数据和写数据的时候都会开启一个线程，而轮询与命令的执行都是主线程。

![3b5TiN](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/3b5TiN.png)

**扩展点二：`memcache`的IO模型**
IO模型本质上是多路复用。与redis不同的是，`memcache`中的IO多路复用是多线程的，并且命令的执行也是多线程的，`memcache`的`acceptor线程`监听到套接字事件之后，丢给`workers线程`，线程负责读写数据并且执行命令

![ixEPN1](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/ixEPN1.png)

**扩展点三：比较redis与memcache**
从redis6.0之后，两者差别不大，**根本的差距在于redis只有一个主线程执行命令，但是memcache是各自的线程执行各自的命令**

引申出的问题：
1. Redis为什么引入多线程模型？IO是多线程的，但是命令的执行还是单线程的
2. Redis一定是单线程的么？6.0之前也不是真正的单线程，比如save是fork一个线程出来的。我们一般说redis是单线程，只是因为在命令执行的时候是单线程的
3. Redis如何保证高性能？

如何引导：
1. 讨论redis为什么那么高效
2. 讨论到多路复用
3. 讨论到IO模型
4. 讨论到"redis一定是单线程么"这种问题
5. 讨论到`memcache`和`redis`的区别


## redis过期处理
> 主要解决的是如何处理Redis中过期的key

[参考](https://blog.csdn.net/u014681799/article/details/113651248)

两种策略：定期删除和惰性删除

![33W6Mo](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/33W6Mo.png)

redis过期处理两种方式：
1. 定期删除：![SbYROR](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/SbYROR.png) 在你设置的那个时间点之前，能删除多少删除多少，这个是为了平衡我们的效率。重点就是**点到即止**
2. 惰性删除：![I820qE](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/I820qE.png)
RDB就是将我们所有的k-v写入到数据库中，然后每次加载RDB的时候我们就不会加载过期的k-v，也就是(RDB不读)
AOF就是压缩合并，避免重写太多，并且重写的时候，会忽略已经过期的key，也就是AOF不写。
从服务器永远不会删除自己的key，它会等到主服务器删除自己的key之后将命令同步到从服务器。

![5Q5sPS](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/5Q5sPS.png)

从服务器上不可能删除自己的key的，因为没有办法通知主服务器也删除这个key。因此当读请求打到主服务器上的时候，如果主服务器发现这个key过期就会删除这个key，但是当读请求打到从服务器上的时候，这个时候就要区分，如果在redis3.2版本之前，从库发现过期也会直接返回，在3.2之后，发现过期直接返回Null，但是需要注意即便是在3.2之后，从库虽然对于过期的key返回null，但是依然不会删除，因为需要等待主库删除之后才会删除这个过期的key。总结就是，从头到尾，从库一直都是等主库的删除才删除，3.2之前不管有没有过期从库都是直接返回值，3.2之后从库如果判断出是过期的key会直接返回Null，但是注意不会删除哦，此时我们需要使用`ttl`命令查看是否过期

类似问题：
1. 为什么redis定期删除策略不删除全部过期的key? 开销太大
2. redis的定时删除策略是怎样的？（重要的是点到即止）
3. redis过期之后，还可以读到数据么？如果是在3.2之前的从库读取的，可以读取到数据
4. 为什么有时候key已经过期了，但是还能读取到数据？用的是redis的3.2版本，并且还是在从服务器上
5. 如何解决redis从库key过期依然返回数据的问题


## redis的内存淘汰机制
> 主要是用来处理，内存快要满的时候如何释放部分内存
eviction是驱逐的意思。volatile
1. noeviction：当内存使用超过配置的时候会返回错误，不会驱逐任何键。
2. allkeys-lru：加入键的时候，如果过限，首先通过LRU算法驱逐最久没有使用的键
3. volatile-lru：加入键的时候如果过限，首先从设置了过期时间的键集合中驱逐最久没有使用的键
4. allkeys-random：加入键的时候如果过限，从所有key随机删除
5. volatile-random：加入键的时候如果过限，从过期键的集合中随机驱逐
6. volatile-ttl：从配置了过期时间的键中驱逐马上就要过期的键
7. volatile-lfu：从所有配置了过期时间的键中驱逐使用频率最少的键
8. allkeys-lfu：从所有键中驱逐使用频率最少的键

lfu：最少频率使用
lru：最近最少使用


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


## redis的数据结构

> redis的数据结构有两种表现形式：

redis数据结构的两种表现形式：
1. 从value的类型来讲：![BCa3ID](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/BCa3ID.jpg)
2. 从底层数据结构来讲：![yKS267](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/yKS267.jpg)x`



**Redis使用的字符串有什么特点：**
![UOXvw0](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/UOXvw0.png)
Redis为什么不直接使用C字符串：直接答出SDS的特性

**Redis的hash table是如何实现的**

[redis中的hashtable介绍](https://blog.csdn.net/u010710458/article/details/80604740)

![aSNSVG](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/aSNSVG.png)
使用拉链法解决冲突，并且冲突的时候会将元素加入到表头，并且redis采用Murmurhash2，该算法效率高，随机性好，可以减少冲突可能。

![mL1MIA](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/mL1MIA.png)

TODO：了解golang的渐进式哈希的过程

特色：rehash过程（对比一下golang的rehash过程）

类似问题：
1. 为什么要使用渐进式rehash，是为了平摊我们的耗时
2. 渐进式rehash有什么缺点？耗时比较大，比如之前我们在一张表里面查，但是后面我们需要在两张表查
3. redis的哈希表扩容有什么特色
4. redis的哈希表如何扩容

**ziplist是如何实现的？有什么用？**
三个要点：连续内存，数据移动，连锁更新

![8udJJ1](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/8udJJ1.png)
![z7vaZg](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/z7vaZg.png)
![v9bqfe](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/v9bqfe.png)
在列表中的表现：![j3MDvn](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/j3MDvn.png)
在字典中的表现：![d0jxZ0](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/d0jxZ0.png)
在有序集合中的表现：![pQH6Xq](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/pQH6Xq.png)


什么时候会触发连锁更新：增删改都会触发

![CnsIfP](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/CnsIfP.png)

**redis的整数集合是什么？有什么特色**
特色：升级不降级
![ztXljO](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/ztXljO.png)


总结：![PDBzrd](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/PDBzrd.png)

> 因为小公司项目 Redis 用的是 String 数据结构。
> 面试官其实想考察的是 Redis 的数据结构，这个时候就应该主动告诉面试官自己知道 Redis 的数据结构！
> 类似问题：redis的数据类型

1. 5种基本数据结构：string，list，hash，set，zset
2. 3种高级数据结构：bitmap、geo、hyperloglog
3. redis5.0引入的数据结构 streams，这是Redis5.0引入的全新数据结构，用一句话概括Streams就是Redis实现的内存版kafka。而且，Streams也有Consumer Groups的概念。通过Redis源码中对stream的定义我们可知，streams底层的数据结构是radix tree：


类似问题：
1. redis底层数据结构 2次
2. redis源码



1. [参考](https://blog.csdn.net/assasin0308/article/details/103965255)
2. [参考](https://juejin.cn/post/6844903644798664712)



## redis内存满了怎么办

1. 修改配置文件
2. 根据redis内存淘汰机制进行缓存淘汰
3. 集群分片存储

## redis未设置过期时间会怎么样
内存满的时候只会根据redis内存淘汰机制进行淘汰，如果淘汰之后依然是满的将会拒绝写入。


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

![O2ktOo](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/O2ktOo.png)
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


5. 微博刷新选取所有关注人的最新 n 条记录如何取。

Redis 和数据库一致性怎么实现？


你对 redis 怎么理解的？

redis 的总体结构

单线程的优势和缺点

redis 的事件分发

讲一讲文件事件有哪些

client 功能是怎么实现的

时间事件（serverCron 函数）

serverCron 做了什么

redis 所有事情都只有一个单线程吗？

bgsave 讲一下，为什么要 fork 一个进程来做


Redis 集群搭建

11：添加一个节点如何分配槽(说详细具体过程)

12：迁移 slot 的过程中，我 get 或者 set 怎么办？

13：Redis 10W 的 QPS 瓶颈点在哪里

Redis 哨兵什么作用，怎么实现的，怎么保证可用的？Redis 为什么单线程？

Redis 底层的跳跃表怎么实现的，哪些提供出来的数据结构用到了？插入查询操作怎么做的？


用过 Redis 的哪几种数据结构？ZSET 是怎么实现的?

20. zrange start, stop, 总长度为 n, 复杂度是多少?