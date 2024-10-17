---
title: 最快的 Go 网络框架 gnet 来啦！
date: 2020-04-02
params:
  author: Andy Pan
summary: gnet 发布正式的 v1 版本，附上权威的 TechEmpower 性能测试结果。
tags:
 - Golang
 - gnet
isCJKLanguage: true
---

![](https://res.strikefreedom.top/static_res/blog/figures/gnet@f2be45.png)

## gnet 是什么？

`gnet` 是一个基于事件驱动的高性能且轻量级的网络框架。它直接使用 [epoll](https://en.wikipedia.org/wiki/Epoll) 和 [kqueue](https://en.wikipedia.org/wiki/Kqueue) 系统调用而非标准 Golang 网络包：[net](https://golang.org/pkg/net/) 来构建网络应用，它的工作原理类似两个开源的网络库：[netty](https://github.com/netty/netty) 和 [libuv](https://github.com/libuv/libuv)。

`gnet` 设计开发的初衷不是为了取代 Go 的标准网络库：[net](https://golang.org/pkg/net/)，而是为了创造出一个类似于 [Redis](http://redis.io)、[Haproxy](http://www.haproxy.org) 能高效处理网络包的 Go 语言网络服务器框架。

`gnet` 的亮点在于它是一个高性能、轻量级、非阻塞的纯 Go 实现的传输层（TCP/UDP/Unix Domain Socket）网络框架，开发者可以使用 `gnet` 来实现自己的应用层网络协议(HTTP、RPC、Redis、WebSocket 等等)，从而构建出自己的应用层网络应用：比如在 `gnet` 上实现 HTTP 协议就可以创建出一个 HTTP 服务器 或者 Web 开发框架，实现 Redis 协议就可以创建出自己的 Redis 服务器等等。

开源地址：[https://github.com/panjf2000/gnet](https://github.com/panjf2000/gnet)

## v1.0.0 正式版本

从 2019 年 9 月份开放源码到 GitHub，经过半年多的新功能开发、bug 修复、架构设计重构以及性能优化，Go 语言网络框架 `gnet` 现在终于发布了第一个正式的 v1 稳定版本！具体的 release 列表可以到 [https://github.com/panjf2000/gnet/releases](https://github.com/panjf2000/gnet/releases) 查看。往后还会持续不断地进行开发、修复、优化甚至重构，如果 `gnet` 的用户在使用的过程中发现 bug，随时到 `gnet` 的 [Github Issue 页](https://github.com/panjf2000/gnet/issues) 给我提 issue。

**目前，`gnet` 具备了如下的功能特性：**

- [X] [高性能](#-%E6%80%A7%E8%83%BD%E6%B5%8B%E8%AF%95) 的基于多线程/Go 程网络模型的 event-loop 事件驱动
- [X] 内置 goroutine 池，由开源库 [ants](https://github.com/panjf2000/ants) 提供支持
- [X] 内置 bytes 内存池，由开源库 [bytebufferpool](https://github.com/valyala/bytebufferpool) 提供支持
- [X] 简洁的 APIs
- [X] 基于 Ring-Buffer 的高效内存利用
- [X] 支持多种网络协议/IPC 机制：TCP、UDP 和 Unix Domain Socket
- [X] 支持多种负载均衡算法：Round-Robin(轮询)、Source Addr Hash(源地址哈希)和 Least-Connections(最少连接数)
- [X] 支持两种事件驱动机制：Linux 里的 epoll 以及 FreeBSD 里的 kqueue
- [X] 支持异步写操作
- [X] 灵活的事件定时器
- [X] SO_REUSEPORT 端口重用
- [X] 内置多种编解码器，支持对 TCP 数据流分包：LineBasedFrameCodec, DelimiterBasedFrameCodec, FixedLengthFrameCodec 和 LengthFieldBasedFrameCodec，参考自 [netty codec](https://netty.io/4.1/api/io/netty/handler/codec/package-summary.html)，而且支持自定制编解码器
- [X] 支持 Windows 平台，基于 ~~IOCP 事件驱动机制~~ Go 标准网络库
- [ ] 实现 `gnet` 客户端

上面列表中除了一些最基本的功能特性，后来的新功能都是由 `gnet` 的用户提出、我开发实现的，在此感谢这些同学的贡献！列表中还有几个计划中的新功能特性在考察和开发阶段，我会对用户提出的新功能需求进行合理性和必要性的评估，然后进行适当的取舍，因此计划中的功能特性列表可能会随时发生变化。另外，也欢迎对 `gnet` 源码感兴趣且想为 `gnet` 增添新功能或者修复 bug 的同学给我提 PR 贡献代码，谢谢！

`gnet` 的自我定位是高性能且轻量级的 Go 语言网络框架，暴露极简的接口的同时又能提供丰富的功能，性能远超 Go 语言原生网络库，如果你的追求极致的性能，那 `gnet` 绝对是你的绝佳选择。

## 性能测试

上面提到 `gnet` 作为一个 Go 语言网络框架主打的是高性能，当然，不能只凭我一张嘴说说就证明了 `gnet` 的高性能，毕竟空口无凭嘛！所以，在这里让我引用改编一下程序员撕逼界著名的一句话：Talk is cheap, show me your benchmark!

提到框架性能测试，熟悉这方面的同学不会没听过 [TechEmpower](https://www.techempower.com/benchmarks/)，这是全球 Web 框架权威性能测试：

> This is a performance comparison of many Web application frameworks executing fundamental tasks such as JSON serialization, database access, and server-side template composition. Each framework is operating in a realistic production configuration. Results are captured on cloud instances and on physical hardware. The test implementations are largely community-contributed and all source is available at the [GitHub repository](https://github.com/TechEmpower/FrameworkBenchmarks).

TechEmpower 测试有源代码，硬件配置全部公开，而且很多框架是作者自己或资深爱好者提交的，他们各自肯定知道该如何极致地优化基于这些框架的 Server，而且这些结果都是可重现的，谁觉得不服可以自己跑跑看，[源代码和需求](https://github.com/TechEmpower/FrameworkBenchmarks/wiki/Project-Information-Framework-Tests-Overview)页面提供了每种测试的执行细节以及其它相关信息，[各种 Web 框架性能对比](https://www.techempower.com/benchmarks/#section=test&runid=c7152e8f-5b33-4ae7-9e89-630af44bc8de&hw=ph&test=plaintext)页面提供了更多有关如何进行测试的细节与测试基准的概况。TechEmpower 测试的主要目的是将目前流行的 Web 开发框架从多个维度来进行测试，这些测试的场景主要是针对这些 Web 框架执行的基本任务，比如数据库访问、JSON 序列化和服务端模板的组合等等场景，整体得分非常具有借鉴价值。

目前已提交 TechEmpower 测试的框架有将近 700 个，其中包括 Netty、Vert.x、Spring、Actix、FastHTTP、Swoole、Nginx 等业界知名的框架/平台，囊括了 C/C++、Java、C#、Rust、Go、PHP、Ruby、Python 等一众主流编程语言，是目前业界最权威的 Web 框架性能测试。

目前，TechEmpower 提供了 2 种硬件环境：云主机 Microsoft Azure D3v2 instances; switched gigabit Ethernet 和物理机 Dell R440 servers each equipped with an [Intel Xeon Gold 5120 CPU](https://ark.intel.com/products/120474/Intel-Xeon-Gold-5120-Processor-19_25M-Cache-2_20-GHz), 32 GB of memory, and an enterprise SSD. Dedicated Cisco 10-gigabit Ethernet switch。

测试内容包括 Plaintext、Single Database Query、Multiple Database Queries、Fortunes、JSON Serialization 等等（全部的测试 cases 可以查看 [Project Information Framework Tests Overview](https://github.com/TechEmpower/FrameworkBenchmarks/wiki/Project-Information-Framework-Tests-Overview#test-types)），得出了一系列的 Web 框架的性能基准，对于程序员来说，这是一份极具参考价值的 Web 框架评估选型的 benchmark 数据。

`gnet` 的性能数据将借助于 TechEmpower 展现，由于 TechEmpower 的测试是基于 HTTP 协议的，因此需要基于 `gnet` 实现一个简单的 HTTP Server 并提交 TechEmpower 测试，目前 `gnet` 参与测试的只有 Plaintext 这一项，这也是最能直接体现出框架网络处理性能的一项测试。

**下面是最新一轮的 TechEmpower Benchmark 性能测试结果：**

```powershell
# Hardware
CPU: 28 HT Cores Intel(R) Xeon(R) Gold 5120 CPU @ 2.20GHz
Mem: 32GB RAM
OS : Ubuntu 18.04.3 4.15.0-88-generic #88-Ubuntu
Net: Switched 10-gigabit ethernet
Go : go1.14.x linux/amd64
```

![](https://res.strikefreedom.top/static_res/blog/figures/techempower-all.jpg)

这是包含全部编程语言框架的性能排名 top 50 的结果，总榜单包含了全世界共计 422 个框架(Plaintext 测试)， `gnet` 位列第 2， `gnet` 也是唯一进入前十的 Go 语言框架。其中，一些业界比较知名的框架/平台的排名：go 原生库 155、netty 排名 45、nginx 排名 83、vert.x 排名 48、spring 排名 270，等等。

![](https://res.strikefreedom.top/static_res/blog/figures/techempower-go.png)

这是 Go 语言分类下的性能排名， `gnet` 位列第 1。

完整的排行可以通过 [view all benchmark results](https://www.techempower.com/benchmarks/#section=test&runid=b24568ff-8eb3-4e5a-816f-8284bd5ec89c&hw=ph&test=plaintext) 查看。

基于上面的 TechEmpower 性能测试结果， `gnet` 在全世界的框架/平台的竞争中名列第 2，~~中二点说法就是天下第二~~，它的高性能定位应该可以说是毋庸置疑了。

P.S. 需要说明的是，因为 `gnet` 并不是一个 Web/HTTP 框架而是一个更加底层的网络框架，所以我给 `gnet` 裸写了一个简单的 HTTP Parser，其对于 HTTP 协议的解析是不完备的，跳过了一些(对于这个测试不需要的)繁杂解析步骤，可以说是针对性的优化。相较于其他真正的 Web/HTTP 框架， `gnet` 在这方面占了一点便宜，不过，Plaintext Benchmarks 主要测试的是框架最**基础/核心**的 `request-routing` 性能，所以最终的测试结果对于评估一个框架的网络处理性能还是极具参考价值和现实意义的。

## 总结

`gnet` 作为一个高性能且轻量级的网络框架，适用于追求极致性能的网络场景，性能表现远超 Go 语言原生网络库，就算是在全球权威性能测试 TechEmpower 排行榜上的表现也很耀眼：全部编程语言总排行第 2，Go 语言分类排行第 1。如果你正在用 Go 开发网络应用且追求极致的性能， `gnet` 将会是你的绝佳选择，欢迎试用！

开源地址：[https://github.com/panjf2000/gnet](https://github.com/panjf2000/gnet)
