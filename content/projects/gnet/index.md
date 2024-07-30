+++
date = 2019-09-07
title = 'Networking framework -- gnet'
summary = '🚀 gnet is a high-performance, lightweight, non-blocking, event-driven networking framework written in pure Go.'
layout = 'page'
tags = ['Gnet', 'Go', 'Open source', 'Announcement']
[params]
  author = 'Andy Pan'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

## 📖 Introduction

### GitHub

https://github.com/panjf2000/gnet

`gnet` is an event-driven networking framework that is ultra-fast and lightweight. It is built from scratch by exploiting [epoll](https://man7.org/linux/man-pages/man7/epoll.7.html) and [kqueue](https://en.wikipedia.org/wiki/Kqueue) and it can achieve much higher performance with lower memory consumption than Go [net](https://golang.org/pkg/net/) in many specific scenarios.

`gnet` and [net](https://golang.org/pkg/net/) don't share the same philosophy about network programming. Thus, building network applications with `gnet` can be significantly different from building them with [net](https://golang.org/pkg/net/), and the philosophies can't be reconciled. There are other similar products written in other programming languages in the community, such as [libevent](https://github.com/libevent/libevent), [libuv](https://github.com/libuv/libuv), [netty](https://github.com/netty/netty), [twisted](https://github.com/twisted/twisted), [tornado](https://github.com/tornadoweb/tornado), etc. which work in a similar pattern as `gnet` under the hood.

`gnet` is not designed to displace the Go [net](https://golang.org/pkg/net/), but to create an alternative in the Go ecosystem for building performance-critical network services. As a result of which, `gnet` is not as comprehensive as Go [net](https://golang.org/pkg/net/), it provides only the core functionalities (in a concise API set) required by a network application and it doesn't plan on being a coverall networking framework, as I think Go [net](https://golang.org/pkg/net/) has done a good enough job in that area.

`gnet` sells itself as a high-performance, lightweight, non-blocking, event-driven networking framework written in pure Go which works on the transport layer with TCP/UDP protocols and Unix Domain Socket. It enables developers to implement their own protocols(HTTP, RPC, WebSocket, Redis, etc.) of application layer upon `gnet` for building diversified network services. For instance, you get an HTTP Server if you implement HTTP protocol upon `gnet` while you have a Redis Server done with the implementation of Redis protocol upon `gnet` and so on.

**`gnet` derives from the project: `evio` with much higher performance and more features.**

## 🚀 Features

- [x] [High-performance](#-performance) event-driven looping based on a networking model of multiple threads/goroutines
- [x] Built-in goroutine pool powered by the library [ants](https://github.com/panjf2000/ants)
- [x] Lock-free during the entire runtime
- [x] Concise and easy-to-use APIs
- [x] Efficient, reusable, and elastic memory buffer: (Elastic-)Ring-Buffer, Linked-List-Buffer and Elastic-Mixed-Buffer
- [x] Multiple protocols/IPC mechanisms: `TCP`, `UDP`, and `Unix Domain Socket`
- [x] Multiple load-balancing algorithms: `Round-Robin`, `Source-Addr-Hash`, and `Least-Connections`
- [x] Flexible ticker event
- [x] `gnet` client
- [x] Running on `Linux`, `macOS`, `Windows`, and *BSD: `Darwin`/`DragonFlyBSD`/`FreeBSD`/`NetBSD`/`OpenBSD`
- [x] **Edge-triggered** I/O support
- [x] Multiple network addresses binding
- [ ] **TLS** support
- [ ] [io_uring](https://kernel.dk/io_uring.pdf) support

***Windows version of `gnet` should only be used in development for developing and testing, it shouldn't be used in production.***

## 🎡 Use cases

The following corporations/organizations use `gnet` as the underlying network service in production.

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
        <a href="https://www.mi.com/global/" target="_blank">
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
