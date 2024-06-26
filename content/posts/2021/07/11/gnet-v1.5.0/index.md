+++
date = 2021-07-11
title = 'Released gnet v1.5.0'
summary = 'Released the official stable version of v1.5.0'
layout = 'page'
tags = ['Gnet', 'Open source', 'Release notes', 'Announcement']
[params]
  author = 'Andy Pan'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

## Features

- Move the logging module out of the internal package and refactor to make it serviceable for users [d56606](https://github.com/panjf2000/gnet/commit/d566061586adfda7efbf58feec0bd8ebf7534479) [b6b1cf](https://github.com/panjf2000/gnet/commit/b6b1cfb53400540000efb0f858d001437bc3d4f9) [8837a9](https://github.com/panjf2000/gnet/commit/8837a92308f41805d38a2377da32530c6c79646d)

## Enhancements

- Support writev and readv in eventloop [f299a8](https://github.com/panjf2000/gnet/commit/f299a8e39a1d5601afc3ddca6eec149e6aa3cf7b)
- Reduce GC pause frequency for Conn.AsyncWrite (#218) [477bb4](https://github.com/panjf2000/gnet/commit/477bb4fe46c38accf993fb11a9ba816bfa9fdc0b)
- Improve logging module [8837a9](https://github.com/panjf2000/gnet/commit/8837a92308f41805d38a2377da32530c6c79646d) [b6b1cf](https://github.com/panjf2000/gnet/commit/b6b1cfb53400540000efb0f858d001437bc3d4f9)
- Refactor the inside AsyncTaskQueue to make it more generic [2d1a46](https://github.com/panjf2000/gnet/commit/2d1a4639d18dbd8faeb43649a3e4859378cc95e6)
- Reduce GC pause frequency for accepting connections [2d1a46](https://github.com/panjf2000/gnet/commit/2d1a4639d18dbd8faeb43649a3e4859378cc95e6)

## Docs

- Add a new user case: Tencent Games [b7ea83](https://github.com/panjf2000/gnet/commit/b7ea839d959face861aca90ea493e5d3f8dfb205)
- Add a new donor [ee965a](https://github.com/panjf2000/gnet/commit/ee965a1453d07300bf14749ea874a5d06ffc660e)

## Misc

- Add debugging log when error occurs in Accept() [#222](https://github.com/panjf2000/gnet/pull/222)
- Make some trivial changes to ring-buffer [1bdd3a](https://github.com/panjf2000/gnet/commit/1bdd3aa77da3827268e1145a07443460e1f01bd5)