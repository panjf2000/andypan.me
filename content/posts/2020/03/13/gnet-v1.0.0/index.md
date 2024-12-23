+++
date = 2020-03-13T09:08:00+09:00
title = 'Released gnet v1.0.0'
summary = 'Released the official stable version of v1.0.0'
layout = 'page'
tags = ['Gnet', 'Open source', 'Release notes']
[params]
  author = 'Andy Pan'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

# Features

- [x] [High-performance](https://github.com/panjf2000/gnet/blob/v1.0.0/README.md#-performance) event-loop under networking model of multiple threads/goroutines
- [x] Built-in load balancing algorithm: Round-Robin
- [x] Built-in goroutine pool powered by the library [ants](https://github.com/panjf2000/ants)
- [x] Built-in memory pool with bytes powered by the library [bytebufferpool](https://github.com/valyala/bytebufferpool)
- [x] Concise APIs
- [x] Efficient memory usage: Ring-Buffer
- [x] Supporting multiple protocols/IPC mechanism: TCP, UDP and Unix Domain Socket
- [x] Supporting two event-driven mechanisms: epoll on Linux and kqueue on FreeBSD
- [x] Supporting asynchronous write operation
- [x] Flexible ticker event
- [x] SO_REUSEPORT socket option
- [x] Built-in multiple codecs to encode/decode network frames into/from TCP stream: LineBasedFrameCodec, DelimiterBasedFrameCodec, FixedLengthFrameCodec and LengthFieldBasedFrameCodec, referencing [netty codec](https://netty.io/4.1/api/io/netty/handler/codec/package-summary.html), also supporting customized codecs
- [x] Supporting Windows platform with ~~event-driven mechanism of IOCP~~ Go stdlib: net
- [ ] Additional load-balancing algorithms: Random, Least-Connections, Consistent-hashing and so on
- [ ] TLS support
- [ ] Implementation of `gnet` Client

