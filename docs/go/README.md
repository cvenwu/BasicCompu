# Go语言相关
!> **Golang**相关的面试题

[参考](https://github.com/KeKe-Li/data-structures-questions/blob/master/src/chapter05/golang.01.md#Go%E4%B8%AD%E5%AF%B9nil%E7%9A%84Slice%E5%92%8C%E7%A9%BASlice%E7%9A%84%E5%A4%84%E7%90%86%E6%98%AF%E4%B8%80%E8%87%B4%E7%9A%84%E5%90%97)

## 基本数据结构
### 数组未初始化能不能求len
!> 这里没有初始化的意思就是没有赋值
**可以，例如 `var a [5]int` 将会赋对应类型的零值**

### 未初始化的slice能不能append
!> 可以，未初始化只是一个为长度和容量都为0的一个切片，使用append将会触发扩容机制
注意：切片未只是声明并未初始化，如`var a []int`，相当于一个nil，可以直接append进行追加元素，将会触发扩容

### go数组和slice的区别
1. 数组定长，定义的时候就需要确定。切片长度不定，append时会自动扩容
2. 相同大小数组可以赋值，会拷贝全部内容。slice赋值和指针一样。数组和slice之间不能相互赋值。当然slice有自己的copy函数
3. 数组也可以进行切片，返回值是一个slice，改变slice时会同步修改数组内容，相当于取得了这个数组的指针
4. slice 的底层数据是数组，slice 是对数组的封装，它描述一个数组的片段。两者都可以通过下标来访问单个元素。
5. 切片的类型和长度无关，而数组的长度是数组类型的一部分。

类似问题：切片和数组区别和底层
[参考1](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-array/)
[参考2](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-array-and-slice/#323-%E8%AE%BF%E9%97%AE%E5%85%83%E7%B4%A0)
[参考](https://zhuanlan.zhihu.com/p/341945051)
[深度解密Go语言之Slice](https://mp.weixin.qq.com/s/MTZ0C9zYsNrb8wyIm2D8BA)


### nil slice 和 empty slice区别

> empty slice用法：当我们查询或者处理一个空的列表的时候，这非常有用，它会告诉我们返回的是一个列表，但是列表内没有任何值。

1. **当我们使用json序列化nil slice的时候将会序列化成null，但是序列化empty slice的时候将会序列化后才能一个[]**
2. 总之，nil slice 和 empty slice是不同的东西,需要我们加以区分的.

```go
package main

import (
	"encoding/json"
	"fmt"
)

/**
 * @Author: yirufeng
 * @Date: 2021/2/16 8:46 上午
 * @Desc:

empty slice用法：当我们查询或者处理一个空的列表的时候，这非常有用，它会告诉我们返回的是一个列表，但是列表内没有任何值。
 **/

type Student struct {
	Id   []int
	Name string
}

func main() {
	var a []int
	b := []int{}
	aByte, _ := json.Marshal(a)
	fmt.Println(string(aByte)) //null
	bByte, _ := json.Marshal(b)
	fmt.Println(string(bByte)) //[]

	c := Student{
		Name: "123",
	}

	d := Student{
		Id:   []int{},
		Name: "123",
	}

	cByte, _ := json.Marshal(c)
	fmt.Println(string(cByte)) //{"Id":null,"Name":"123"}

	dByte, _ := json.Marshal(d)
	fmt.Println(string(dByte)) //{"Id":[],"Name":"123"}
}

```

### slice底层实现
[参考1](https://jiajunhuang.com/articles/2020_05_23-go_slice.md.html)
[参考2](https://jiajunhuang.com/articles/2017_07_18-golang_slice.md.html)
[参考3](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-array-and-slice/#323-%E8%AE%BF%E9%97%AE%E5%85%83%E7%B4%A0)
[参考](https://zhuanlan.zhihu.com/p/341945051)
[参考4](https://mp.weixin.qq.com/s/MTZ0C9zYsNrb8wyIm2D8BA)

类似问题：slice的底层原理
类似问题：切片怎么扩容,扩容过程中需不需要重新写入

### map的底层原理
[参考1](https://jiajunhuang.com/articles/2017_07_27-golang_map.md.html)
[参考2](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-hashmap/)
[参考3](https://zhuanlan.zhihu.com/p/341945051)
[参考4](https://mp.weixin.qq.com/s/2CDpE5wfoiNXm1agMAq4wA)
类似问题：go map结构实现，并发安全否

### Map是线程安全的吗？怎么解决并发安全问题
map不是线程安全的，需要使用sync.Map，可以看[该文章的最后](https://jiajunhuang.com/articles/2017_07_27-golang_map.md.html)

### go 常见数据结构
数组，切片，映射，通道，[所有的讲解参考](https://zhuanlan.zhihu.com/p/341945051)

### Slice、map都是安全的吗
> slice与map都不是线程安全的

### Golang中的 map 以及 slice 的源码分析以及slice内存泄漏，需要了解什么是内存泄漏

### 线程安全的map锁分段的细节

----------
## channel 管道
[参考](https://mp.weixin.qq.com/s/90Evbi5F5sA1IM5Ya5Tp8w)
channel是线程安全的

### channel 有缓冲 无缓冲
!> [参考](https://www.flysnow.org/2017/04/17/go-in-action-go-channel.html)
1. 注意点：如果一个通道被关闭了，我们就不能往这个通道里发送数据了，如果发送的话，会引起painc异常。但是，我们还可以接收通道里的数据，如果通道里没有数据的话，接收的数据是零值.
2. 无缓冲的通道指的是通道的大小为0，也就是说，这种类型的通道在接收前没有能力保存任何值，它要求发送goroutine和接收goroutine同时准备好，才可以完成发送和接收操作。
3. 从上面无缓冲的通道定义来看，发送goroutine和接收gouroutine必须是同步的，同时准备后，如果没有同时准备好的话，先执行的操作就会阻塞等待，直到另一个相对应的操作准备好为止。这种无缓冲的通道我们也称之为同步通道。
4. 有缓冲通道内部有一个类似于队列机制的缓冲区，定义的时候通过make的第2个参数指定缓冲区大小，如果容量满了，接收将会阻塞，如果缓冲区空，发送将会阻塞。
5. 如果给定了一个缓冲区容量，那么通道就是异步的，只要缓冲区有未使用空间用于发送数据，或还包含可以接收的数据，那么其通信就会无阻塞地进行
类似问题： go的channel 有缓冲无缓冲如何定义，区别 

### 关闭channel读取后会怎样

### golang中channel调用问题

### go 同步，channel的实现
----------
## 内存泄漏
### goroutine内存泄漏场景
[参考](https://blog.csdn.net/leeright/article/details/94466831)
goroutine泄漏描述：如果你启动了一个 goroutine，但并没有符合预期的退出，直到程序结束，此goroutine才退出，这种情况就是 goroutine 泄露。
![IR4yAK](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/IR4yAK.png)
[参考](https://mp.weixin.qq.com/s/90Evbi5F5sA1IM5Ya5Tp8w)

### go中导致内存泄漏的原因
![IR4yAK](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/IR4yAK.png)
[参考](https://mp.weixin.qq.com/s/90Evbi5F5sA1IM5Ya5Tp8w)

### 了解内存泄漏吗？有什么危害？
![IR4yAK](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/IR4yAK.png)
[参考](https://mp.weixin.qq.com/s/90Evbi5F5sA1IM5Ya5Tp8w)

### go 内存分配
[参考](https://github.com/KeKe-Li/data-structures-questions)

----------
## 协程与并发

### Golang中哪些方式可以安全读写共享变量
1. 加互斥锁或读写锁
2. 使用channel进行安全读写共享变量 [参考](https://studygolang.com/articles/17835?fr=sidebar)
3. 使用原子性操作
[参考](https://www.bilibili.com/read/cv5694992/)

### go并发为什么快
[参考](https://zhuanlan.zhihu.com/p/111346689)

### go协程 java线程区别

### sync.Once的实现原理
!>（上次哔哩哔哩面试问到了，幸亏我事后看了一眼是类似双重检验锁的实现方式哦），让我写出来，我写出了一大半，还让我运行一下，我运行不出来

1. [参考](https://mp.weixin.qq.com/s/gT9bNsNhU189PsURJyzsmQ)

### context包有没有用过，我说没用过

### sync.Map 怎么解决线程安全问题？看过源码吗？

### golang 的 waitGroup 用法

### Go的协程可以不可以自己让出cpu

### Go的协程可以只挂在一个线程上面吗

### 一个协程挂起换入另外一个协程是什么过程？

### 有一个高并发的场景该怎么处理

### golang 协程机制

### 协程的栈空间大小有限制吗？会主动扩展吗？

### golang context 应用场景

### context 的数据结构（树）

### go 协程

### go 协程怎么切换的

### Golang 的协程与 Java 线程的区别？
协程是轻量级线程，多个协程可以由一个或多个线程管理。
协程无需上下文切换，没有线程之间切换的开销。
协程的调度不需要多线程的锁机制，因为只有一个线程，不存在同时写变量冲突，执行效率比多线程高很多。
这题面试的时候没有答到第三点。
### Golang 的协程间通讯方式有哪些？
共享内存和协程通信。
「Don’t communicate by sharing memory, share memory by communicating」所以更提倡使用 channel 进行通信。
当时答的时候也只答了 channel，面试官说还有一种，怎么都想不起来Orz。
### Go里面一个协程能保证绑定在一个内核线程上面的。

### go多线程

### golang协程i/o多路复用机制

----------
## GMP与垃圾回收
### go垃圾回收

### go gmp 调度 4次

### go 垃圾回收，什么时候触发 2次

### GMP源码级别分析

### go 内存逃逸分析（分析了栈帧，讲五种例子，描述堆栈优缺点，点头）

----------

## 性能问题排查
### golang 性能问题怎么排查？（profile）

### 项目调试（讲了下GDB）

### gdb

----------
## 异常处理
### defer recover 的问题（自己了解不多，简单介绍）

### go defer

### defer的执行顺序

### defer A ; defer B ; defer panic("") A和B能不能执行到

### defer recover panic 执行顺序

----------
## go相关的其他问题


### go怎样实现继承

### 逃逸分析讲一下

### socket

### copy是操作符还是内置函数
> 内置函数

[其他内置函数可以参考golang包中的builtin function](https://studygolang.com/pkgdoc)

### 一道很简单的Go题目，Go怎么做深拷贝。
1. 深拷贝（Deep Copy）：拷贝的是数据本身，创造一个样的新对象，新创建的对象与原对象不共享内存，新创建的对象在内存中开辟一个新的内存地址，新对象值修改时不会影响原对象值。既然内存地址不同，释放内存地址时，可分别释放。值类型的数据，默认全部都是深复制，Array、Int、String、Struct、Float，Bool。
2. 浅拷贝（Shallow Copy）：拷贝的是数据地址，只复制指向的对象的指针，此时新对象和老对象指向的内存地址是一样的，新对象值修改时老对象也会变化。释放内存地址时，同时释放内存地址。引用类型的数据，默认全部都是浅复制，Slice，Map。

### golang有什么设计很巧妙的地方吗，举几个例子。

### 实现string ，拷贝构造，主要内存开辟析构（没答好)

### go语言的性能的优劣

### Golang 的默认参数传递方式以及哪些是引用传递？
默认采用值传递，且Go 中函数传参仅有值传递一种方式。
slice、map、channel 都是引用类型。
slice 能够通过函数传参后，修改对应的数组值，因为 slice 内部保存了引用数组的指针，并不是因为引用传递。
这题回答的时候以为有引用传递，答了 slice、map、channel 都是引用传递，结果一百度，发现是用起来像引用传递，其实都是值传递，就像 slice 传递的是指针的复制。

### go相关知识点（内存分配、go优缺点、go错误处理有什么优缺点）
一个GO源码级别的仓库讲解：https://github.com/bereborn/learn/blob/master/go/go%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D.c
--------------
## 框架问题

### gin框架如何实现，我说用go内置的net http包实现的

### 了解中间件吗?有什么好处

### etcd 原理（讲了下raft协议）

### RPC


-------------

## new 和 make区别

Golang 语言中的内置函数 make 和 new 都是用作变量初始化，但是它们初始化变量的方式不同。关于它们之间的区别，我们可以简述为 make 返回类型是引用类型，new 返回类型是指针类型。本文我们首先分别介绍二者，然后再介绍二者的区别。
02
内置函数 make
关于内置函数 make，官方的介绍是 make 内置函数仅用作分配内存空间并初始化 slice，map 和 chan 类型的对象。与 new 相同，第一个参数是类型，而不是值。与 new 不同，make 的返回类型与其参数的类型相同，而不是指向它的指针。
func make(t Type, size ...IntegerType) Type
返回值取决于传参的类型：
Slice
s := make([]T, 0, 10)
以上示例代码表示分配一个长度为 10 的底层数组，返回一个长度为 0，容量为 10 的切片。
使用内置函数 make 初始化 slice，第一个参数是类型，第二个参数是 slice 的长度，第三个参数是可选参数，它代表 slice 的容量，如果不传入第三个参数，slice 的容量与长度相同，但是如果传入第三个参数，它的值（容量）比如大于或等于传入的第二个参数（长度）。
Map
m := make(map[T]T)
以上示例代码表示给 map 分配内存空间。
使用内置函数 make 初始化 map，传入的参数是类型，map 没有容量限制，初始化时无需指定容量的大小。
Channel
c := make(chan T, 10)
以上示例代码表示给 channel 分配的内存空间大小（缓冲容量）为 10。channel 的缓冲区使用指定的值初始化缓冲容量。如果为零或忽略大小(不传入第二个参数)，则 channel 为无缓冲的。
03
内置函数 new
关于内置函数 new，官方介绍是内置函数 new 仅用作分配内存空间，第一个参数是类型，而不是值，返回值是指向新分配该类型的零值的指针。
func new(Type) *Type
在 Golang 开发中，通常不太常用内置函数 new，它的使用场景一般是需要显式返回指针。
04
make 和 new 的区别
在阅读完上述内容后，我相信读者朋友们应该已经了解了二者的区别。
make 仅用于初始化 slice，map 和 chan，new 可用于初始化任意类型。
make 返回值是”引用类型“，new 返回值是指针类型。
05
总结
本文我们介绍了内置函数 make 和 new，并且对比归纳了二者的区别，在 Golang 开发中，内置函数 make 是必用的，因为 slice，map 和 chan，必须使用内置函数 make 初始化，才可以使用；而内置函数 new 并不常用，通常使用场景是需要显式返回指针。

[参考](https://www.kancloud.cn/aceld/golang/1958307)



## 为什么要使用 Go 语言？Go 语言的优势在哪里？
> 从自己理解的角度看

1. 代码简单，只有25个关键字，例如每个语句最后不需要加上分号
2. 跨平台编译，如果你写的Go代码不包含cgo，那么就可以做到window系统编译linux的应用，如何做到的呢？Go引用了plan9的代码，这就是不依赖系统的信息。
3. 便于部署：指定编译的平台架构以及运行的平台就可以生成对应的二进制文件
4. 可以支持很大量的并发，因为goroutine占用的内存很小。Go语言的运行环境（runtime）会在goroutine需要的时候动态地分配栈空间，而不是给每个goroutine分配固定大小的内存空间。这样就避免了需要程序员来决定栈的大小。分块式的栈是最初Go语言组织栈的方式。当创建一个goroutine的时候，它会分配一个8KB的内存空间来给goroutine的栈使用。我们可能会考虑当这8KB的栈空间被用完的时候该怎么办?已经用完了分配的栈空间。如果是的话，它会调用morestack函数

## go func 使用注意点：博大群里面提出的问题
cyan：
1.闭包引用
2.多协程竞争（同步原语，比如锁，waitgroup 等等）
3.匿名函数内的遍历是否逃逸


博大：我总结了一下，欢迎大家补充
1. 需要关注下是否泄露，泄露的情况有哪些，可以配合uber的leak库检测一下 https://github.com/uber-go/goleak
2. 要想着检测到泄露要如何优雅地关闭goroutine。
3. goroutine的量是否可控 ，这里推荐下潘少的
https://github.com/panjf2000/ants，感兴趣的同学也可以看看其他goroutine pool https://awesome-go.com/#goroutines


## go语言中的坑 （泫提出的）
![1QuwdT](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/1QuwdT.png)
还有个坑是 
type Node struct{
 Val int
}
var test map[string]Node

test["a"] 拿到的Node是不能修改的
即test["a"].Val = 1
会报错
正确的写法是 var test map[string]*Node



gc 算法



深拷贝，浅拷贝