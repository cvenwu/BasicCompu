# Go语言相关

!> **Golang**相关的面试题


1. GMP源码级别分析
2. Golang中的 map 以及 slice 的源码分析以及slice内存泄漏，需要了解什么是内存泄漏
3. mysql索引
4. go 内存逃逸分析（分析了栈帧，讲五种例子，描述堆栈优缺点，点头）
5. defer recover 的问题（自己了解不多，简单介绍）
6. mysql 索引慢分析（线上开启slowlog，提取慢查询，然后仔细分析explain 中 tye字段以及extra字段，发生的具体场景及mysql是怎么做的，被表扬回答的不错）
7. mysql 分布式id（项目用到的）
8. redis 跳表 
9. redis hashtable实现，什么时候退化成ziplist
10. ziplist 和 hashtable 之间区别
11. gdb
12. go 协程
13. 项目调试（讲了下GDB）
14. redis 跳表 同8
15. go 同步，channel的实现
16. go 协程怎么切换的
17. go 内存分配
18. select、epoll
19. tcp 四次挥手
20. TCP相关的关闭连接
21. TCP最多有几个连接（fd限制+四元组ip、port限制）
22. 服务端出现大量close_wait原因（主子进程共享socket，close不能完全关闭）
23. mysql 隔离级别、sql慢查询
24. go defer
25. etcd 原理（讲了下raft协议）
26. sql语句：查询所有课程分数都大于或等于80分的学生姓名
27. 检验字符串是一个回文字符串
28. 给定一个字符串 s，找到 s 中最长的回文子串
29. go相关知识点（内存分配、go优缺点、go错误处理有什么优缺点）


一个GO源码级别的仓库讲解：https://github.com/bereborn/learn/blob/master/go/go%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D.c