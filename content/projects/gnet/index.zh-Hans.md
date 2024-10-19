+++
date = 2019-09-07
title = '网络框架 —— gnet'
summary = '🚀 gnet 是一个高性能、轻量级、非阻塞、事件驱动的 Go 网络框架'
layout = 'page'
tags = ['Gnet', 'Go', '开源', 'Announcement']
[params]
  author = '潘少'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

# 📖 简介

[`gnet`](https://github.com/panjf2000/gnet) 是一个基于事件驱动的高性能和轻量级网络框架。这个框架是基于 [epoll](https://en.wikipedia.org/wiki/Epoll) 和 [kqueue](https://en.wikipedia.org/wiki/Kqueue) 从零开发的，而且相比 Go [net](https://golang.org/pkg/net/)，它能以更低的内存占用实现更高的性能。

`gnet` 和 [net](https://golang.org/pkg/net/) 有着不一样的网络编程模式。因此，用 `gnet` 开发网络应用和用 [net](https://golang.org/pkg/net/) 开发区别很大，而且两者之间不可调和。社区里有其他同类的产品像是 [libuv](https://github.com/libuv/libuv), [netty](https://github.com/netty/netty), [twisted](https://github.com/twisted/twisted), [tornado](https://github.com/tornadoweb/tornado)，`gnet` 的底层工作原理和这些框架非常类似。

`gnet` 不是为了取代 [net](https://golang.org/pkg/net/) 而生的，而是在 Go 生态中为开发者提供一个开发性能敏感的网络服务的替代品。也正因如此，`gnet` 在功能全面性上比不了 Go [net](https://golang.org/pkg/net/)，它只会提供网络应用所需的最核心的功能和最精简的 APIs，而且 `gnet` 也并没有打算变成一个无所不包的网络框架，因为我觉得 Go [net](https://golang.org/pkg/net/) 在这方面已经做得足够好了。

`gnet` 的卖点在于它是一个高性能、轻量级、非阻塞的纯 Go 语言实现的传输层（TCP/UDP/Unix Domain Socket）网络框架。开发者可以使用 `gnet` 来实现自己的应用层网络协议(HTTP、RPC、Redis、WebSocket 等等)，从而构建出自己的应用层网络服务。比如在 `gnet` 上实现 HTTP 协议就可以创建出一个 HTTP 服务器 或者 Web 开发框架，实现 Redis 协议就可以创建出自己的 Redis 服务器等等。

**`gnet` 衍生自另一个项目：`evio`，但拥有更丰富的功能特性，且性能远胜之。**

# 🚀 功能

## 🦖 当前支持

- [x] 基于多线程/协程网络模型的[高性能](#-性能测试)事件驱动循环
- [x] 内置 goroutine 池，由开源库 [ants](https://github.com/panjf2000/ants) 提供支持
- [x] 整个生命周期是无锁的
- [x] 简单易用的 APIs
- [x] 高效、可重用而且自动伸缩的内存 buffer：(Elastic-)Ring-Buffer, Linked-List-Buffer and Elastic-Mixed-Buffer
- [x] 多种网络协议/IPC 机制：`TCP`、`UDP` 和 `Unix Domain Socket`
- [x] 多种负载均衡算法：`Round-Robin(轮询)`、`Source-Addr-Hash(源地址哈希)` 和 `Least-Connections(最少连接数)`
- [x] 灵活的事件定时器
- [x] `gnet` 客户端支持
- [x] 支持 `Linux`, `macOS`, `Windows` 和 *BSD 操作系统: `Darwin`/`DragonFlyBSD`/`FreeBSD`/`NetBSD`/`OpenBSD`
- [x] **Edge-triggered** I/O 支持
- [x] 多网络地址绑定

## 🕊 未来计划

- [ ] 支持 **TLS**
- [ ] 支持 [io_uring](https://github.com/axboe/liburing/wiki/io_uring-and-networking-in-2023)
- [ ] 支持 **KCP**

***`gnet` 的 Windows 版本应该仅用于开发阶段的开发和测试，切勿用于生产环境***。

# 🎡 用户案例

以下公司/组织在生产环境上使用了 `gnet` 作为底层网络服务。

<table>
  <tbody>
    <tr>
      <td align="center" valign="middle">
        <a href="https://www.tencent.com/">
          <img src="https://res.strikefreedom.top/static_res/logos/tencent_logo.png" width="200" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.tencentgames.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/tencent-games-logo.jpeg" width="200" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.iqiyi.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/iqiyi-logo.png" width="200" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.mi.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/mi-logo.png" width="200" />
        </a>
      </td>
    </tr>
    <tr>
      <td align="center" valign="middle">
        <a href="https://www.360.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/360-logo.png" width="200" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://tieba.baidu.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/baidu-tieba-logo.png" width="200" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.jd.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/jd-logo.png" width="200" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.zuoyebang.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/zuoyebang-logo.jpeg" width="200" />
        </a>
      </td>
    </tr>
  </tbody>
</table>

如果你也正在生产环境上使用 `gnet`，欢迎提 Pull Request 来丰富这份列表。