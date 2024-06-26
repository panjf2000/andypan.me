+++
date = 2021-12-05
title = 'Released gnet v1.6.0'
summary = 'Released the official stable version of v1.6.0'
layout = 'page'
tags = ['Gnet', 'Open source', 'Release notes', 'Announcement']
[params]
  author = 'Andy Pan'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

# gnet client is now available for production!

## Features

- Add a new event handler: AfterWrite() [6a654c](https://github.com/panjf2000/gnet/commit/6a654c85e7c1172503813c9703603e42eea2fc29)
- Implement the gnet client [2295e8](https://github.com/panjf2000/gnet/commit/2295e8c6f3394341d28318cb6ea33f0799d52c45) [a5ac95](https://github.com/panjf2000/gnet/commit/a5ac95a5057fb82e2f71cb6a7f4ffed83c967efb) [4db46d](https://github.com/panjf2000/gnet/commit/4db46da43d5defd5da71213c0abaebb174af642c) [802fa3](https://github.com/panjf2000/gnet/commit/802fa358f2c8ac95414e36cb0afd53f6dd57bfa0) [7159b9](https://github.com/panjf2000/gnet/commit/7159b95cd9ebc8fe2f9bea909844eb8c8bb37bf7)
- Implement writev and readv on BSD-like OS's [60ba6d](https://github.com/panjf2000/gnet/commit/60ba6d30b04351e26c3f7c9cc496b1b849936731)
- Implement a mixed buffer of ring-buffer and list-buffer [edbdf4](https://github.com/panjf2000/gnet/commit/edbdf4b54b7439bfb2ac4ba9652ec6a1764e0659)
- Invoke OnClosed() when a UDP socket is closed [7be4b2](https://github.com/panjf2000/gnet/commit/7be4b2a758e32af489450b6b62d8da48e471ba00)
- Implement the gnet.Conn.AsyncWritev() [9a2032](https://github.com/panjf2000/gnet/commit/9a2032f876cd8f41c554545bcbb63d3043f4946f)

## Enhancements

- Prevent the event-list from expanding or shrinking endlessly [b220df](https://github.com/panjf2000/gnet/commit/b220dfd3f3ff9b8ecee4a09170d4db3760393fc0)
- Reduce the potential system calls for waking pollers up [9ce41f](https://github.com/panjf2000/gnet/commit/9ce41f3b921a9341081506629185e733f97defa4)
- Eliminate the code for preventing false-sharing [0bfade](https://github.com/panjf2000/gnet/commit/0bfade3aea015a7932b0e45b646a6c85a620a205)
- Support so_reuseaddr [#280](https://github.com/panjf2000/gnet/pull/280)
- Make several improvements for logger [58d203](https://github.com/panjf2000/gnet/commit/58d2031440b1c9725e2d12aeb651aa8bc78d3489)
- Optimize the buffer management and network I/O [6aba6d](https://github.com/panjf2000/gnet/commit/6aba6d7a3fc31cf749b0001dcb1c82f01c816f65)
- Improve the project layout [2e172b](https://github.com/panjf2000/gnet/commit/2e172bde78bcdb56dbec9a57d95dfa4b6213b1f2)
- Improve the logic of reading data from socket into ring-buffer [a7f07b](https://github.com/panjf2000/gnet/commit/a7f07b3d4eaa70a9b5c8b389d73b72ddb06b8c16)
- Get as much data read from socket per loop as possible [148ee1](https://github.com/panjf2000/gnet/commit/148ee163fb3ddd0fcd7919ab17390a3cd910933f)
- Improve the network read with ring-buffer and readv [0dcf59](https://github.com/panjf2000/gnet/commit/0dcf599fd0673bc712b5409fd9a0711cb90606c0)
- Avoid memory allocations when calling readv [15611b](https://github.com/panjf2000/gnet/commit/15611b482f50f1333fcee47b02d6ec04b4d2ede5)
- Refactor the logic of handling UDP sockets [d72d3d](https://github.com/panjf2000/gnet/commit/d72d3de70a0cb31c6059820dbd4ba6db6c4e23eb)
- Make the mixed-buffer more flexible [d72d3d](https://github.com/panjf2000/gnet/commit/4ac906cae698b1a4483c583d0267f86f05ce595b)
- Improve the management logic of the mixed-buffer [b8d571](https://github.com/panjf2000/gnet/commit/b8d571dd762cb79c2c685f16d36886f6edb40195)

## Bugfixes

- Resolve the data race of stdConn on Windows [#235](https://github.com/panjf2000/gnet/pull/235)
- Fix the data corruption in some default codecs [a56d2f](https://github.com/panjf2000/gnet/commit/a56d2f3f50981107ae6b2bd2653fe19dc75d4e18)
- Fix the issue of panic: runtime error: slice bounds out of range [30311e](https://github.com/panjf2000/gnet/commit/30311e936869d8685c8c06ff98170f0adb68bc8b)

## Docs

- Update the benchmark data [21f55a](https://github.com/panjf2000/gnet/commit/21f55a6832d82b88073c51ccfbed8a0e627399c3) [24e4ce](https://github.com/panjf2000/gnet/commit/24e4ce06a4c4e1d3990eec9945c98175763c027f) [1b4ae5](https://github.com/panjf2000/gnet/commit/1b4ae56edf45bb3bc165c183a089fb0a8144ca67) [81d984](https://github.com/panjf2000/gnet/commit/81d984236401fb42d2f75c8989b87321804f4503)
- Add the echo benchmarks on macOS [f429e7](https://github.com/panjf2000/gnet/commit/f429e7afaf3745574c95bf03d60baeaec2ecd9c1)
- Change the license from MIT to Apache 2.0 [a900c8](https://github.com/panjf2000/gnet/commit/a900c8f21958eb8096443125afafb672d9f1218e)

## Misc

- Add a new patron [0c9f96](https://github.com/panjf2000/gnet/commit/0c9f965f24a6a706ddcfbcc2ba2dd8339e611e8e)
- Create FUNDING.yml [1989ed](https://github.com/panjf2000/gnet/commit/1989eda4cc668e548f8572ac9fb07cef8c8f612d) [7b2979](https://github.com/panjf2000/gnet/commit/7b29795db5fe184da0939490f8bf4ec39d3c27db)
- Remove the irrelevant articles [bbdc1b](https://github.com/panjf2000/gnet/commit/bbdc1bcc76138feb3529d639e63ebe9374c22165)
- Correct the wrong logging function [10c619](https://github.com/panjf2000/gnet/commit/10c619f3a42c4f8397464a7a45daff24bfa873ea)