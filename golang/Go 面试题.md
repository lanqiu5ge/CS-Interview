# Go 面试题
## 基础语法
### 1. select 是随机选择还是顺序的？
> Keywords：Go并发编程，select

#### 解析
select 会**随机**选择一个可用通道作收发

#### 引申
- select 底层实现？
- select语句中阻塞场景？

#### 参考资料
[Go select](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-select/)



### 2. Go语言局部变量分配在栈还是堆？
> Keywords：Go逃逸分析

#### 解析
Go语言编译器会自动决定把一个变量放在栈还是放在堆，编译器会做逃逸分析，当发现变量的作用域没有跑出函数范围，就可以在栈上，反之则必须分配在堆。

#### 参考材料
【饶大】[Golang之变量去哪儿](https://qcrao.com/2019/03/01/where-go-variables-go/)
[go语言局部变量分配在栈还是堆](https://www.jianshu.com/p/4e3478e9d252)

### 3. 简述一下你对Go垃圾回收机制的理解？
> Keywords: Go内存管理, Go垃圾回收
#### 解析
v1.1 STW
v1.3 Mark STW, Sweep 并行
v1.5 三色标记法
v1.8 hybrid write barrier(混合写屏障：优化STW)

#### 参考材料
[Golang 垃圾回收剖析](http://legendtkl.com/2017/04/28/golang-gc/)
[Go GC](https://qcrao91.gitbook.io/go/gc/gc)
[Go 垃圾收集器](https://draveness.me/golang/docs/part3-runtime/ch07-memory/golang-garbage-collector/)

### 4. 简述一下golang的协程调度原理?
> Keywords: Go调度器

#### 解析
M(machine): 代表着真正的执行计算资源，可以认为它就是os thread（系统线程）。
P(processor): 表示逻辑processor，是线程M的执行的上下文。
G(goroutine): 调度系统的最基本单位goroutine，存储了goroutine的执行stack信息、goroutine状态以及goroutine的任务函数等。

#### 参考材料
[go语言调度器源代码情景分析](https://mp.weixin.qq.com/mp/homepage?__biz=MzU1OTg5NDkzOA==&hid=1&sn=8fc2b63f53559bc0cee292ce629c4788&scene=1&devicetype=iOS14.1&version=1700112a&lang=zh_CN&nettype=WIFI&ascene=7&session_us=gh_8b5b60477260&fontScale=100&wx_header=1)
[深度解密调度器源码系列](https://qcrao.com/2019/09/06/dive-into-go-scheduler-source-code/)

### 5. Golang 语言 runtime 机制？
> Keywords: Go runtime

#### 解析
Runtime 负责管理任务调度，垃圾收集及运行环境。同时，Go提供了一些高级的功能，如goroutine, channel, 以及Garbage collection。这些高级功能需要一个runtime的支持. runtime和用户编译后的代码被linker静态链接起来，形成一个可执行文件。这个文件从操作系统角度来说是一个user space的独立的可执行文件。 从运行的角度来说，这个文件由2部分组成，一部分是用户的代码，另一部分就是runtime。runtime通过接口函数调用来管理goroutine, channel及其他一些高级的功能。从用户代码发起的调用操作系统API的调用都会被runtime拦截并处理。
Go runtime的一个重要的组成部分是goroutine scheduler。他负责追踪，调度每个goroutine运行，实际上是从应用程序的process所属的thread pool中分配一个thread来执行这个goroutine。因此，和java虚拟机中的Java thread和OS thread映射概念类似，每个goroutine只有分配到一个OS thread才能运行。
![1030286042a3cda6edc1c97110b8dbc1.png](evernotecid://E2048F6E-45CB-4A35-91A6-3BFFE1250286/appyinxiangcom/23641386/ENNote/p231?hash=1030286042a3cda6edc1c97110b8dbc1)

#### 参考资料

### 6. 如何获取 go 程序运行时的协程数量, gc 时间, 对象数, 堆栈信息?

> Keywords: Go debug

#### 解析
调用接口 runtime.ReadMemStats 可以获取以上所有信息, 
注意: 调用此接口会触发 STW(Stop The World)

#### 参考资料

### 7. 介绍下你平时都是怎么调试 golang 的 bug 以及性能问题的?
> Keywords: Go性能调优, Go Debug

#### 解析
panic 调用栈
pprof
火焰图(配合压测)
使用go run -race 或者 go build -race 来进行竞争检测
查看系统 磁盘IO/网络IO/内存占用/CPU 占用(配合压测)

#### 参考资料
- go pprof

### 8. 简单介绍下 golang 中 make 和 new 的区别?
> Keywords: make, new

#### 解析
new(T) 是为一个 T 类型的新值分配空间, 并将此空间初始化为 T 的零值, 并返回这块内存空间的地址, 也就是 T 类型的指针 *T, 该指针指向 T 类型值占用的那块内存. 
make(T) 返回的是初始化之后的 T, 且只能用于 slice, map, channel 三种类型. make(T, args) 返回初始化之后 T 类型的值, 且此新值并不是 T 类型的零值, 也不是 T 类型的指针 *T, 而是 T 类型值经过初始化之后的引用.

#### 参考资料
[Go中的make和new的区别](https://www.cnblogs.com/ghj1976/archive/2013/02/12/2910384.html)
[《Go语言设计与实现》make 和 new](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-make-and-new/)

### 9. Go defer 考查
> Keywords: defer

写出下面代码输出内容：
```go
package main

import (
    "fmt"
)

func main() {
    defer_call()
}

func defer_call() {
    defer func() { fmt.Println("打印前") }()
    defer func() { fmt.Println("打印中") }()
    defer func() { fmt.Println("打印后") }()

    panic("触发异常")
}
```
输出：
```
打印后
打印中
打印前
panic: 触发异常
```
#### 解析
考点：defer执行顺序
解答：defer 是后进先出。 panic 需要等defer 结束后才会向上传递。 出现panic恐慌时候，会先按照defer的后入先出的顺序执行，最后才会执行panic。

#### 参考资料
[Golang之轻松化解defer的温柔陷阱](https://qcrao.com/2019/02/12/how-to-keep-off-trap-of-defer/)
[【深入】defer 链表如何被遍历执行](https://qcrao.com/2020/03/23/how-to-traverse-defer-links/)
[《Go语言设计与实现》defer](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-defer/)






## 参考材料
[【Go 夜读】Golang语言](https://reading.hidevops.io/interview/interview-golang-language/)