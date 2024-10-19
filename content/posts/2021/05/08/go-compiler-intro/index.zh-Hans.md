---
title: 【转】漫谈Go语言编译器（01）
date: 2021-05-08
params:
  author: 史斌
summary: Go 编译器初探
tags:
 - Golang
 - Go Compiler
 - Go 编译器
isCJKLanguage: true
---

编者荐语：

Go 语言编译器重磅贡献者同时也是 LLVM 项目 Committer -- [史斌大神](https://github.com/benshi001)，倾情奉献的深入理解 Go 语言编译器系列文章开始连载，首发于我们的 ["Golang Contributor Club"](https://golangcn.org) 公众号。

---

## 开场白

我（史斌）曾在Gopher China 2020大会上分享过[《Go语言编译器简介》](https://github.com/gopherchina/conference/tree/master/2020)。

当时仅仅是泛泛的浏览了一下Go编译器，但其实每一页PPT都能单独展开讲。因此我准备写一系列文章，把当时未能详细阐述的内容补充一下。

欢迎转载扩散，但请标注原创出自公众号："Golang Contributor Club"。

## 为什么学习编译器

编译器对多数人是一个黑盒，只需了解命令行参数即可；但我认为深入了解编译器的内部有很多好处。

第一个好处是：了解编译器能让gopher深入了解Go程序的运行机制，从而写出更优质的代码。

第二个好处是：能提升gopher的内功。编译器涉及正则表达式，树搜索/树变换，有向图变换/有向图搜索；这些对于写上层业务逻辑的gopher以前是虚幻的屠龙术，但是阅读编译器源码后，你会发现它们其实很实用。

第三个好处是：你有机会接触汇编语言，以及各个硬件平台的特点。ARM64服务器已经成为一种趋势，已经在部分场景取代了X86_64服务器。所以我们需要了解不同的硬件平台的特点。

第四个好处是：编译器会是未来的一个热门行业。因为美国的制裁，当下是国内芯片行业的春天，加强中国芯的建设已经成为一个政治任务。而处理器（CPU/GPU/NPU/TPU/.../xPU）作为芯片领域的重要分支，是需要编译器支持的。当前国内编译器领域严重缺人，因为入门难度高，起薪自然比其它容易入门的领域高。（我的团队长期招人，欢迎砸简历。）

第五个好处是：你可以基于Go编译器定制自己的编程语言。Go编译器从go-1.7开始已经实现模块化，这意味着你可以自己设计一个全新的编程语言（甚至不兼容Go语法），借助Go编译器编译成一个可执行程序。

## 阅读前的准备

虽然本系列的定位是科普文，但是我也不准备从最基本的正则表达式，语法树，有向图等最基础的知识讲起；因此假设读者有一定的知识基础。

在这个前提下，我希望你看过我在Gopher China 2020上的讲座，并阅读过[PPT](https://github.com/gopherchina/conference/tree/master/2020)。

除此之外，我希望你阅读过柴树杉大神写的[《Go语法树入门》](https://github.com/chai2010/go-ast-book)，这是关于编译前端非常优秀的入门书籍。在此基础上，本系列的重点是讲解编译中端和后端。

坦白地说，编译器确实是最难入门的领域；同时也是最难写科普文的领域：专业人士看了觉得浅显，没相关基础的读者看了觉得云山雾罩。虽然如此，我还是想挑战一下。希望能收到读者更多反馈，我会据此调节讲解的内容。

## 基础知识回顾

目前成熟的生产环境用的编译器，都是基于久经考验的前中后三阶段架构。

![](https://res.strikefreedom.top/static_res/blog/figures/5de6ceac-1a18-4cdf-8b05-6d0503a93300.png)

其中前端将高级语言的源代码翻译成IR（Intermediate Representation）序列，并传递给中端；中端对输入的原始IR序列做通用优化，并输出优化后的IR序列给后端；后端接收中端传来的IR序列，将其映射成真正的汇编指令序列，并做进一步和硬件相关的特殊优化。最终经过链接生成可执行程序。

这种架构的第一个好处是：新的高级语言无需支持所有的硬件，仅需生成IR即可；新的硬件无需适配所有的高级编程语言，仅需适配IR即可。从因果关系上看，前端和后端各自都是一个子编译器，前端把高级语言编译成IR序列，IR对于前端就是（伪）汇编；而后端把IR编译成真汇编，IR对于后端就是（伪）高级语言。

这种架构的第二个好处是：避免重复性的优化。例如把'a*8'优化成'a<<3'在所有的硬件上都适用。因此没必要每个后端都做一遍，把这个优化放在中端一次性完成即可。

这种架构的第三个好处是：针对SSA（Single Static Assignment）形态的IR，已经有无数计算机科学家做了大量细致的研究，有非常成熟的优化算法可以借鉴。编译原理最经典的教材龙书的作者，就因为在此领域的开创性贡献获得了2021年度的图灵奖。

## Go编译器

Go编译器在go-1.7之前，采用的是非常老旧的语法树（AST）覆盖的编译技术，从go-1.7开始逐步采用更主流的基于SSA-IR的前中后三阶段编译技术。虽然Go编译器无需支持别的高级编程语言，但是上述的第二点和第三点好处仍然适用。

一个额外的好处是，Go编译器的中端和后端被做成了独立的库"golang.org/x/tools/go/ssa"。这就意味着，你可以自己写一个新的语言前端（甚至不兼容Go语法），并调用Go的ssa库，生成可执行程序；甚至于你自己定义的编程语言可以无缝地调用其它Go的代码；进一步，你可以借助于Go的生态系统打造自己的编程语言！类似于Scala语言和Java语言的关系。

## SSA-IR

SSA-IR（Single Static Assignment）是一种介于高级语言和汇编语言的中间形态的伪语言，从高级语言角度看，它是（伪）汇编；而从真正的汇编语言角度看，它是（伪）高级语言。

顾名思义，SSA（Single Static Assignment）的两大要点是：

  * Static：每个变量只能赋值一次（因此应该叫常量更合适）。

  * Single：每个表达式只能做一个简单运算，对于复杂的表达式a _b+c_ d要拆分成："t0=a _b; t1=c_ d; t2=t0+t1;"三个简单表达式。

例如有如下Go源代码：

```go
func foo(a, b int) int {
  c := 8
  return a*4 + b*c
}
```

它改写成SSA形式是：

```go
func foo(a, b int) int {
  c := 8
  t0 := a * 4
  t1 := b * c
  t2 := t0 + t1
  return t2
}
```

它被中端优化后的SSA形式是：

```go
func foo(a, b int) int {
  t0 := a << 2
  t1 := b << 3
  t2 := t0 + t1
  return t2
}
```

说到这里，敏感的读者可能会问：如果只有赋值，那么程序岂不是只有顺序结构，而分支结构和循环结构是如何支持的？因此这里要提前剧透一点，Go编译器的IR不仅有SSA，还有if-goto指令；Go语言的if语句和for循环语句，都是会被编译成if-goto指令。

事实上在结构化编程（顺序/分支/循环）概念出现之前，就是因为goto语句的滥用而导致了1960年代的第一次软件工程危机。说白了程序运行实际上还是依赖goto，而if/for语句只是为了让程序更具有可读性，减少潜在bug。下面是一个小例子：

```go
func foo(a, b int) int {
  if (a > b) {
    return a + b
  } else {
    return a - b
}
```

它被编译器前端翻译成IR后是如下形态（注意：if-goto-else-goto是一个整体的单条指令，条件/真目的地/假目的地是它的三个操作数，就像除法指令有被除数和除数两个操作数一样）：

```go
func foo(a, b int) int {
  c := a > b
  if (c) goto _true; else goto _false;

_true:
  t0 := a + b
  return t0

_false:
  t1 := a - b
  return t1
}
```

后续文章会逐步对Go的IR做完整的介绍。这里举两个最简单的例子，便于读者快速了解IR的核心概念和常见形态。

## 实操

Go编译器提供了完备的调试手段，正好我们可以借用过来展示Go编译器的内部工作流程。本系列文章使用go-1.14.15做演示，请读者安装此版本。

下面用一个例子test.go：

```go
// test.go
package main

func foo(a, b int) int {
  c := 8
  return a*4 + b*c
}

func main() {
  println(foo(100, 150))
}
```

使用如下命令编译，在得到可执行目标程序的同时，还会得到一个额外的ssa.html，这个ssa.html就记录了Go编译器的工作流程和各阶段的中间结果。其中GOSSAFUNC环境变量用于指定需要被调试的函数，本例中是foo。

```bash
$ GOSSAFUNC=foo go build a.go
# command-line-arguments
dumped SSA to ./ssa.html
```

打开ssa.html，可以看到编译foo函数一共经过了40多道工序。

![](https://res.strikefreedom.top/static_res/blog/figures/d275d197-e3a1-4b0a-9b85-3dacfadff1dd.png)

其中，source和AST属于编译器前端，从start到writebarrier属于编译器中端，从lower到genssa属于编译器后端。

这其中的start/writebarrier/genssa三道工序的输出，请读者认真看一下。

start工序是编译器前端的最后一步，输出原始IR序列，请读者对照源码仔细体会。v10对应变量c，v11对应第一个乘法运算的乘数4，v12是第一个乘法的积，v13是第二个乘法的积，v14是加法的和。

```asm
start

b1:
v1 (?) = InitMem <mem>
v2 (?) = SP <uintptr>
v3 (?) = SB <uintptr>
v4 (?) = LocalAddr <*int> {a} v2 v1
v5 (?) = LocalAddr <*int> {b} v2 v1
v6 (?) = LocalAddr <*int> {~r2} v2 v1
v7 (4) = Arg <int> {a} (a[int])
v8 (4) = Arg <int> {b} (b[int])
v9 (?) = Const64 <int> [0]
v10 (?) = Const64 <int> [8] (c[int])
v11 (?) = Const64 <int> [4]
v12 (6) = Mul64 <int> v7 v11
v13 (6) = Mul64 <int> v8 v10
v14 (6) = Add64 <int> v12 v13
v15 (6) = VarDef <mem> {~r2} v1
v16 (6) = Store <mem> {int} v6 v14 v15
Ret v16 (+6)
```

writebarrier是编译器中端的最后一步，输出经通用优化后的IR序列。读者可以看到，最初的乘法运算（Mul64）被优化成了移位运算（Lsh64x64）。

```asm
writebarrier [549 ns]

b1:
v1 (?) = InitMem <mem>
v2 (?) = SP <uintptr>
v6 (?) = LocalAddr <*int> {~r2} v2 v1
v7 (4) = Arg <int> {a} (a[int])
v8 (4) = Arg <int> {b} (b[int])
v15 (6) = VarDef <mem> {~r2} v1
v9 (+6) = Const64 <uint64> [2]
v5 (6) = Const64 <uint64> [3]
v12 (+6) = Lsh64x64 <int> [false] v7 v9
v13 (6) = Lsh64x64 <int> [false] v8 v5
v14 (6) = Add64 <int> v12 v13
v16 (6) = Store <mem> {int} v6 v14 v15
Ret v16 (+6)
```

而genssa是后端的最后一步，输出真正的最优的x86_64汇编序列。

小结和展望 本文介绍了理解Go编译器的所需背景知识，以及Go编译器的整体工作流程。后续的文章会逐步针对上面的各道工序展开讲解。

## 原文

[漫谈Go语言编译器（01）—— Golang Contributor Club](https://mp.weixin.qq.com/s/0q0k8gGX56SBKJvfMquQkQ)