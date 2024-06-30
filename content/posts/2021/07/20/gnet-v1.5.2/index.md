+++
date = 2021-07-20
title = 'Released gnet v1.5.2'
summary = 'Released the official stable version of v1.5.2'
layout = 'page'
tags = ['Gnet', 'Open source', 'Release notes']
[params]
  author = 'Andy Pan'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

## Enhancements

### About poller

By default, `gnet` utilizes the standard package `golang.org/x/sys/unix` to implement pollers with `epoll` or `kqueue`, where a HASH MAP of `fd->conn` is introduced to help retrieve connections by file descriptors returned from pollers, but now the user can run `go build` with build tags `poll_opt`, like this: `go build -tags=poll_opt`, and `gnet` then switch to the optimized implementations of pollers that invoke the system calls of `epoll` or `kqueue` directly and add file descriptors to the interest list along with storing the corresponding connection pointers into `epoll_data` or `kevent`, in which case `gnet` can get rid of the HASH MAP of `fd->conn` and regain each connection pointer by the conversion of `void*` pointer in the I/O event-looping. In theory, it ought to achieve a higher performance with this optimization.

See [#230](https://github.com/panjf2000/gnet/pull/230) for code details.

### About ring-buffer

Adjust the growth strategy of ring-buffer, make it go more smoothly rather than simply doubling up (which may waste a huge amount of memory) in each buffer scaling. [ccd37f](https://github.com/panjf2000/gnet/commit/ccd37ff254e47ed4fff3feee79d88849d9444502) [fd8d27](https://github.com/panjf2000/gnet/commit/fd8d27ba98237a44c8d37ca9c9ecf02a7365ad44)