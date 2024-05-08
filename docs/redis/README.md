# Redis面试题整理

!> **Redis** 就数据结构 内存淘汰机制 单线程 高可用 缓存应用

## 数据结构系列





| 对象类型 | 编码类型   | 要求 | 编码转换 | 图例 | 集合命令实现方法 |
| ----- | --------- | ----------- | ------- | ------- | ------- |
| 字符串对象 | int |     如果一个字符串对象保存的是整数值，并且这个整数值可以用long类型来表示，那么字符串对象会将整数值保存在字符串对象结构的ptr/属性里面（将void*转换成long），并将字符串对象的编码设置为int。        |  
int编码的字符串对象和embstr编码的字符串对象在条件满足的情况下，会被转换为raw编码的字符串对象。
可以用long double类型表示的浮点数在Redis中也是作为字符串值来保存的。
在有需要的时候，程序会将保存在字符串对象里面的字符串值转换回浮点数值，执行某些操作，然后再将执行操作所得的浮点数值转换回字符串值，并继续保存在字符串对象里面。

因为Redis没有为embstr编码的字符串对象编写任何相应的修改程序（只有int编码的字符串对象和raw编码的字符串对象有这些程序），所以embstr编码的字符串对象实际上是只读的。当我们对embstr编码的字符串对象执行任何修改命令时，程序会先将对象的编码从embstr转换成raw，然后再执行修改命令。因为这个原因，embstr编码的字符串对象在执行修改命令之后，总会变成一个raw编码的字符串对象。       |   ![](https://raw.githubusercontent.com/cvenwu/ImageHosting/master/WindowsPicGo/202405082226384.png)  | ![](https://raw.githubusercontent.com/cvenwu/ImageHosting/master/WindowsPicGo/202405082227977.png)    |
| 字符串对象 | content 2 |             |         |         |         |
| 字符串对象 | content 2 |             |         |         |         |
| 字符串对象 | content 2 |             |         |         |         |

<h1>123</h1>


----------

面试题列表：
1. Redis 底层的跳跃表怎么实现的，哪些提供出来的数据结构用到了？插入查询操作怎么做的？
2. 讲一下Redis数据结构
3. 用过 Redis 的哪几种数据结构？ZSET 是怎么实现的?
4. Zrange命令 start, stop, 总长度为 n, 复杂度是多少?
5. Redis 源码

Redis数据结构的两种表现形式：
1. 从value的类型来讲：![BCa3ID](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/BCa3ID.jpg)
2. 从底层数据结构来讲：![yKS267](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/yKS267.jpg)

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

1. [参考](https://blog.csdn.net/assasin0308/article/details/103965255)
2. [参考](https://juejin.cn/post/6844903644798664712)

有序集合是什么：我说了排序，然后问怎么排序的 然后又问有序集合zset的时间复杂度
zset实现原理：[参考](https://www.jianshu.com/p/360627bd04e5)、 [【推荐】参考](https://www.cnblogs.com/aspirant/p/11475295.html)
zset如何实现有序：图来自于<<redis设计与实现>>8.6。![dSnA7L](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/dSnA7L.png)
zset的时间复杂度
Redis 跳表
Redis HashTable实现，什么时候退化成ziplist
ziplist和HashTable之间区别

## 内存系列

### 面试题：Redis内存满了怎么办
1. 修改Redis内存：
   1. 方法一：修改Redis启动使用的配置文件`maxmemory <bytes>`，并重新启动
   2. 方法二：进入Redis命令行终端，通过命令来进行设置

```shell
// 获取设置的Redis能使用的最大内存大小
127.0.0.1:6379> config get maxmemory
3) "maxmemory"
4) "0"
// 设置Redis最大占用内存大小为100M
127.0.0.1:6379> config set maxmemory 100mb
```

2. 根据内存淘汰策略进行淘汰
   1. 方法一：修改Redis启动使用的配置文件中的内容maxmemory-policy <策略>，并重新启动。优点是重启 Redis 服务后配置不会丢失，缺点是必须重启 Redis 服务，设置才能生效。
   2. 方法二：进入Redis命令行终端，通过命令来进行设置。优点是设置之后立即生效，不需要重启 Redis 服务，缺点是重启 Redis 之后，设置就会失效。

```shell
127.0.0.1:6379> config get maxmemory-policy
3) "maxmemory-policy"
4) "noeviction"
127.0.0.1:6379> config set maxmemory-policy allkeys-lru
```
3. 搭建或者扩充Redis集群进行分片存储
4. 对代码进行优化：
  a. 去除不必要的数据
  b. 改进使用的数据结构


### 面试题：Redis未设置过期时间会怎么样
随着时间的推移，内存会逐渐飙升直到内存满为止。内存满的时候会根据内存淘汰机制进行淘汰，如果淘汰之后依然是满的将会拒绝写入。

### 面试题：Redis内存淘汰机制
> 用于当内存快要满的时候如何释放部分内存。

Redis 内存淘汰策略共有八种，这八种策略大体分为「不进行数据淘汰」和「进行数据淘汰」两类策略。
1. 不进行数据淘汰的策略
    - `noeviction`（Redis 3.0之后，默认的内存淘汰策略） ：它表示当运行内存超过最大设置内存时，不淘汰任何数据，这时如果有新的数据写入，会报错通知禁止写入，不淘汰任何数据，但是如果没用数据写入的话，只是单纯的查询或者删除操作的话，还是可以正常工作。
2. 进行数据淘汰的策略：针对「进行数据淘汰」这一类策略，又可以细分为「在设置了过期时间的数据中进行淘汰」和「在所有数据范围内进行淘汰」这两类策略。
    - 在设置了过期时间的数据中进行淘汰：
        - `volatile-random`：随机淘汰设置了过期时间的任意键值（当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个 key）
        - `volatile-ttl`：优先淘汰更早过期的键值（当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的 key 优先移除）
        - `volatile-lru`（Redis 3.0 之前，默认的内存淘汰策略）：淘汰所有设置了过期时间的键值中，最久未使用的键值（当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的 key）
        - `volatile-lfu`（Redis 4.0 后新增的内存淘汰策略）：淘汰所有设置了过期时间的键值中，最少使用的键值；
    - 在所有数据范围内进行淘汰：
        - `allkeys-random`：随机淘汰任意键值（当内存不足以容纳新写入数据时，在键空间中，随机移除某个 key）
        - `allkeys-lru`：淘汰整个键值中最久未使用的键值；（当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 key（**最常用**））
        - `allkeys-lfu`（Redis 4.0 后新增的内存淘汰策略）：淘汰整个键值中最少使用的键值。

![](https://raw.githubusercontent.com/cvenwu/ImageHosting/master/WindowsPicGo/202405082218841.png)

!> **Tips**：eviction是驱逐的意思。volatile在英文中表示易挥发，在Redis中表示设置了过期时间的key

!> **Tips**：LFU（Least Frequently Used）：最少频率使用，通过统计访问频率，将访问频率最少得键值对淘汰

!> **Tips**：LRU（Least Recently Used）：最近最少使用，将最近最少使用的数据淘汰


**拓展问题**
- 为什么手动淘汰 Redis 中的数据，不使用 Redis 的内存淘汰机制：因为 Redis 的内存淘汰机制是对 Key 值进行筛选，而红包池的机制是对 Value 值的筛选。面试官提及了内存淘汰机制，应该主动告诉面试官自己知道 Redis 的内存淘汰机制！

注意事项：Redis的淘汰算法实际实现上并非针对所有key，而是抽样一部分并且从中选出被淘汰的key


### 面试题：Redis过期处理方式
> 这里主要探讨的是如何处理Redis中过期的key，Redis中选择【惰性删除+定期删除】两种策略配和使用

过期删除策略：
1. 定时删除（特点：随机选择，点到为止）：在设置 key 的过期时间时，同时创建一个定时事件，当时间到达时，由事件处理器自动执行 key 的删除操作。![](https://raw.githubusercontent.com/cvenwu/ImageHosting/master/WindowsPicGo/202405082032080.png)
    - 优点：可以保证过期 key 会被尽快删除，也就是内存可以被尽快地释放。因此，定时删除对内存是最友好的。
    - 缺点：在过期 key 比较多的情况下，删除过期 key 可能会占用相当一部分 CPU 时间，在内存不紧张但 CPU 时间紧张的情况下，将 CPU 时间用于删除和当前任务无关的过期键上，无疑会对服务器的响应时间和吞吐量造成影响。所以，定时删除策略对 CPU 不友好。
2. 惰性删除：不主动删除过期键，每次从数据库访问 key 时，都检测 key 是否过期，如果过期则删除该 key。![](https://raw.githubusercontent.com/cvenwu/ImageHosting/master/WindowsPicGo/202405082033075.png)
    - 优点：因为每次访问时，才会检查 key 是否过期，所以此策略只会使用很少的系统资源，因此，惰性删除策略对 CPU 时间最友好。
    - 缺点：如果一个 key 已经过期，而这个 key 又仍然保留在数据库中，那么只要这个过期 key 一直没有被访问，它所占用的内存就不会释放，造成了一定的内存空间浪费。所以，惰性删除策略对内存不友好。
3. 定期删除：每隔一段时间「随机」从数据库中取出一定数量的 key 进行检查，并删除其中的过期key。
    - 优点：通过限制删除操作执行的时长和频率，来减少删除操作对 CPU 的影响，同时也能删除一部分过期的数据减少了过期键对空间的无效占用。
    - 缺点：
        - 内存清理方面没有定时删除效果好，同时没有惰性删除使用的系统资源少。
        - 难以确定删除操作执行的时长和频率。如果执行的太频繁，定期删除策略变得和定时删除策略一样，对CPU不友好；如果执行的太少，那又和惰性删除一样了，过期 key 占用的内存不会及时得到释放。

**参考**
1. [Redis 过期删除策略是什么？](https://xiaolincoding.com/redis/module/strategy.html#redis-%E8%BF%87%E6%9C%9F%E5%88%A0%E9%99%A4%E7%AD%96%E7%95%A5%E6%98%AF%E4%BB%80%E4%B9%88)
2. [Redis缓存过期处理与内存淘汰机制](https://blog.csdn.net/u014681799/article/details/113651248)

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

**扩展问题：**
1. 为什么redis定期删除策略不删除全部过期的key? 开销太大
2. redis的定时删除策略是怎样的？（重要的是点到即止）
3. redis过期之后，还可以读到数据么？如果是在3.2之前的从库读取的，可以读取到数据
4. 为什么有时候key已经过期了，但是还能读取到数据？用的是redis的3.2版本，并且还是在从服务器上
5. 如何解决redis从库key过期依然返回数据的问题

--------

## 线程模型

!> 我们通常说，Redis是单线程，主要是指Redis的网络IO和键值对读写是由一个线程来完成的，这也是Redis对外提供键值存储服务的主要流程。但Redis的其他功能，比如持久化、异步删除、集群数据同步等，其实是由额外的线程执行的。所以，严格来说，Redis并不是单线程，但是我们一般把Redis称为单线程高性能，这样显得“酷”些。接下来，我也会把Redis称为单线程模式。而且，这也会促使你紧接着提问：“为什么用单线程？为什么单线程能这么快？”

**扩展问题**
1. Redis的IO模型
2. Redis为什么引入多线程模型？IO是多线程的，但是命令的执行还是单线程的
3. Redis一定是单线程的么？6.0之前也不是真正的单线程，比如save是fork一个线程出来的。我们一般说redis是单线程，只是因为在命令执行的时候是单线程的
4. Redis如何保证高性能（Redis为什么那么高效、Redis单线程模型为什么可以那么快）
5. 多路复用
6. IO模型
7. Redis所有事情都只有一个单线程么
8. 单线程的优缺点
9. 同时对Redis和数据库进行内存更新的时候，如何保证操作的成功 / Redis更新后线程挂掉了怎么办？[Redis的读更新和写更新-如何保证Redis与数据库的数据一致性](https://blog.csdn.net/qq32933432/article/details/108690254)、[Redis缓存系列--(六)缓存和数据库一致性更新原则]（https://www.cnblogs.com/mr-ziyoung/p/14048326.html）
10. Redis是单线程模型还是多线程模型
11. Redis 为什么单线程？

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

--------

## 持久化


### 面试题：Redis 持久化有哪几种，主要用来存什么数据，Redis 崩了怎么办（持久化没答上来， 说这是运维的工作 ，崩了说的哨兵，顺带说了下集群）

### 面试题：Redis 怎么做持久化

### 面试题：bgsave 讲一下，为什么要 fork 一个进程来做

### 面试题：写时修改



## 高可用

### 面试题：Redis 集群搭建
### 面试题：Redis 哨兵什么作用，怎么实现的，怎么保证可用的
### 面试题：添加一个节点如何分配槽(说详细具体过程)
### 面试题：迁移 slot 的过程中， get 或者 set 怎么办？
### 面试题：如何保证多个服务器的数据一致性

## 缓存应用

1. 怎么做限流

[什么是Redis缓存雪崩、穿透、击穿，十分钟给你讲的明明白白](https://www.bilibili.com/video/BV1f5411b7ux?from=search&seid=16353605540017451556)
缓存雪崩：大量的redis缓存在同一时间内过期，导致请求直接打到数据库上造成数据库崩溃
缓存


| 应用 | 描述   | 解决措施 | title 4 |
| ----- | --------- | ----------- | ------- |
| 内容1 | content 2 |             |         |
| 行3  | line3     | column 3    |         |

### 面试题：微博刷新选取所有关注人的最新 n 条记录如何取

- 缓存雪崩：

## 缓存雪崩怎么解决
正常的缓存流程：![sTLgm3](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/sTLgm3.png)

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

## 扩展

### 面试题：你对 redis 怎么理解的？


### 面试题：Redis 的总体结构

### 面试题：Client 功能是怎么实现的

### 面试题：Redis的事件分发

### 面试题：讲一下文件事件有哪些

### 面试题：Redis 和数据库一致性怎么实现？

### 面试题：时间事件（serverCron 函数）

### 面试题：serverCron 做了什么

### 面试题：Redis 10W 的 QPS 瓶颈点在哪里

## 参考资料

1. **重点书籍**：《Redis 设计与实现》
2. [图解Redis介绍](https://xiaolincoding.com/redis/)
3. 极客时间《Redis 核心技术与实战》