# redis
!> **redis** 面试题以及答案整理

## redis的数据结构
> 因为小公司项目 Redis 用的是 String 数据结构。
> 面试官其实想考察的是 Redis 的数据结构，这个时候就应该主动告诉面试官自己知道 Redis 的数据结构！
类似问题：redis的数据类型

5种基本数据结构：string，list，hash，set，zset
3种高级数据结构：bitmap、geo、hyperloglog
redis5.0引入的数据结构 streams，这是Redis5.0引入的全新数据结构，用一句话概括Streams就是Redis实现的内存版kafka。而且，Streams也有Consumer Groups的概念。通过Redis源码中对stream的定义我们可知，streams底层的数据结构是radix tree：

[参考](https://blog.csdn.net/assasin0308/article/details/103965255)

## redis内存淘汰机制
> 为什么手动淘汰 Redis 中的数据，不使用 Redis 的内存淘汰机制？
因为 Redis 的内存淘汰机制是对 Key 值进行筛选，而红包池的机制是对 Value 值的筛选。
面试官提及了内存淘汰机制，应该主动告诉面试官自己知道 Redis 的内存淘汰机制！
同时对Redis和数据库进行内存更新的时候，如何保证操作的成功 / Redis更新后线程挂掉了怎么办？

1. noeviction: 当内存不足以容纳新写入数据时，新写入操作会报错，这个一般没人用吧，实在是太恶心了
2. allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 key（这个是最常用的）
3. allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个 key，这个一般没人用吧，为啥要随机，肯定是把最近最少使用的 key 给干掉啊。
4. volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的 key（这个一般不太合适）
5. volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个 key
6. volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的 key 优先移除

![znfHJ6](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/znfHJ6.png)
## 有序集合是什么 我说了排序，然后问怎么排序的 然后又问有序集合zset的时间复杂度

## redis是单线程模型还是多线程模型
进阶问题：redis单线程模型为什么可以那么快（说了非阻塞IO的实现selector，定时任务的实现）
项目里面用redis来干什么？说了一下购物车的实现

## zset实现原理

## zsset如何实现有序

## redis 持久化
## redis底层数据结构 2次
## zset的时间复杂度
## redis源码
## redis 跳表 
## redis hashtable实现，什么时候退化成ziplist
## ziplist 和 hashtable 之间区别


