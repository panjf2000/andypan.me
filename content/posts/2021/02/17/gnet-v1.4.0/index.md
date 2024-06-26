+++
date = 2021-02-17
title = 'Released gnet v1.4.0'
summary = 'Released the official stable version of v1.4.0'
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

- Support TCP_NODELAY socket option [525df8](https://github.com/panjf2000/gnet/commit/525df8ed1e734ac8d9a8f7fc4d28853a3f1e9cb3)
- Implement the lock-free queue for dispatching tasks faster [#181](https://github.com/panjf2000/gnet/pull/181)

## Enhancements

- Shrink the ring-buffer for saving memory [14fd04](https://github.com/panjf2000/gnet/commit/14fd04a041994e35393a2abf5c039db4e9f29f60)
- Improve event poll [fc042c](https://github.com/panjf2000/gnet/commit/fc042ccde2d57697eafe278b2d6d9c056246e251)
- Improve the buffered channels [83b96e](https://github.com/panjf2000/gnet/commit/83b96eda98cb0ea311d776537a502151a029dab6)
- Shrink the poll event list when it need to [158763](https://github.com/panjf2000/gnet/commit/158763823c0c155bd3750c18d58cf887c7f2a813)
- Improve the I/O in event-loop [c554f4](https://github.com/panjf2000/gnet/commit/c554f4ec298f76c6bb5710a62db710161189d239)
- Prioritize writable events [0f08c8](https://github.com/panjf2000/gnet/commit/0f08c8f351c1cb60e83db1b6edf1babdb853767d)
- Make it more robust when running async jobs [6509b8](https://github.com/panjf2000/gnet/commit/6509b85eca7847abf8919829857e05851a408d0c)
- Improve the poller waking logic [4d8acc](https://github.com/panjf2000/gnet/commit/4d8accba64f9c8e9da621409c5fa42d1aa3bea51)

## Bugfixes

- Re-enqueue the subsequent jobs when a error occurs [1af3f6](https://github.com/panjf2000/gnet/commit/1af3f6c4734f41f424de4b929407ed401ddbc830)
- Close and release a socket when error occurs [9d86c9](https://github.com/panjf2000/gnet/commit/9d86c92a224a3ce0b670070dd4256099c0dedcff)
- Check outbound buffer in case the socket has been released [035f61](https://github.com/panjf2000/gnet/commit/035f6140a187f58c62d3d9e023f22fbdb81cd66f)
- Avoid starving sockets [e31525](https://github.com/panjf2000/gnet/commit/e3152523da481e8e59f1ac137e89b5c669287010)

## Docs

- Enrich the doc about using UDP packets [0047c1](https://github.com/panjf2000/gnet/commit/0047c15c6ccf98fddf985bbb254ab22abd2463ce)
- Add more user cases of gnet [076b16](https://github.com/panjf2000/gnet/commit/076b16c68f52de65fb059ec9b81d03f0e7ef06f4)
- Update benchmark results [028bab](https://github.com/panjf2000/gnet/commit/028bab284f63babd203b44dc59e2e0bde3ee2691)
- Update the description about ring-buffer [38b086](https://github.com/panjf2000/gnet/commit/38b086af35cf57e9bc08f3655eefacab7985dd5a)

## Misc

- Add one more donor [12bfa5](https://github.com/panjf2000/gnet/commit/12bfa5cfa7742859ad75f8117d3deadebeb7f12c)
- Refine code [2aee5a](https://github.com/panjf2000/gnet/commit/2aee5a9ea2d86d2eabf2964be96cbf8ce643bc48)
- Add comments on those core code [2404ed](https://github.com/panjf2000/gnet/commit/2404edde0031ac52bf1dcb803708e347080b9e47)
- Refine code and add comments [4ecbc5](https://github.com/panjf2000/gnet/commit/4ecbc501c2cfa05773e338cd07d967bb1d0afad5)
- Add a warning comment for OnOpened [0bddd0](https://github.com/panjf2000/gnet/commit/0bddd003d7d8934df7ab65389c8975e155c72440)