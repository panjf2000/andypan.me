+++
date = 2020-09-13
title = 'Released gnet v1.3.0'
summary = 'Released the official stable version of v1.3.0'
layout = 'page'
tags = ['Gnet', 'Open source', 'Release notes']
[params]
  author = 'Andy Pan'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

## Features

- Determine logging mode by environment variable [2a7977](https://github.com/panjf2000/gnet/commit/2a7977feb33ba18f0c9d788dc611e85154df8548)
- Support locking each I/O event-loop goroutine to an OS thread [6fd641](https://github.com/panjf2000/gnet/commit/6fd6413920618f8cd9906923bd5768a43ce1c3b7)

## Enhancements

- Improve the implementation of Least-Connections load-balancing [b6a5f5](https://github.com/panjf2000/gnet/commit/b6a5f564a9eea9c3fbb977c5ef3c8163689a63d0)
- Speed up the Least-Connections load-balancing [b5fbbd](https://github.com/panjf2000/gnet/commit/b5fbbdac59588572b4a6e8c26bb6049cfd9b7211)
- Leverage system calls instead of `net` package to create listeners [ccc8c6](https://github.com/panjf2000/gnet/commit/ccc8c649f380c546242251911040c8935a2cbb57)
- Refine the logging module [f46da4](https://github.com/panjf2000/gnet/commit/f46da440d6435831558a1e8cbbd3f20a5ed2d773)
- Reorg errors in gnet [1c2f4b](https://github.com/panjf2000/gnet/commit/1c2f4b0c5127a4f40b5f635d604212a33e45a220)
- Move logging module into internal package [00af50](https://github.com/panjf2000/gnet/commit/00af504ed00ab0ecb6477956ef4fe81f1dadd66e)
- Take off the codec from eventloop [bc85c3](https://github.com/panjf2000/gnet/commit/bc85c34e5f347fa055b5fea1ecb84fbbdf85ef91)
- Refactor for buffer in Conn [455c5a](https://github.com/panjf2000/gnet/commit/455c5ae5241e0875fc79a9338fefc38b4a5c17bc)
- Refactor the load-balancing algorithm of source addr hash [abbf58](https://github.com/panjf2000/gnet/commit/abbf5820c1d264fa22e8bf8d00755d6093a86767)
- Make channel buffered if necessary [43f93c](https://github.com/panjf2000/gnet/commit/43f93ca57010dbb9b87f07928400301e487af65e)

## Bugfixes

- Fix a bug that led to negative counter of sync.WaitGroup on Windows [16ed4a](https://github.com/panjf2000/gnet/commit/16ed4ab6719b70b396b47d5d51c1f70f44e1205a)
- Add 'windows' build tag under SO_REUSEPORT mode [7862f3](https://github.com/panjf2000/gnet/commit/7862f3e2cb60554928c2d22f66213916dd69dbee)
- Supplement all the formal protocol formats of TCP and UDP [258253](https://github.com/panjf2000/gnet/commit/258253c7813a6577e338561f0c3da1bd7c5e57da)
- Resolve the issue of closing one fd twice [b5a5c7](https://github.com/panjf2000/gnet/commit/b5a5c715ca07f8b4e3c371a8460274e77953bbdf)
- Fix concurrent loopCloseConn causes panic [#111](https://github.com/panjf2000/gnet/pull/111)
- Eliminate duplicate calls to loopCloseConn [a72f5f](https://github.com/panjf2000/gnet/commit/a72f5fd30757fa51cad4ad10c9583468c450b68e)

## Docs

- Add a customized codec example [#90](https://github.com/panjf2000/gnet/pull/90)
- Add placeholder for user cases and new relevant article [5197f1](https://github.com/panjf2000/gnet/commit/5197f186792670851ddcfdbe94b5317196e3ef6a)
- Renew results of techempower benchmark [61eee8](https://github.com/panjf2000/gnet/commit/61eee8fa29105002d2e1af17d2c1afc54001c6be)
- Add the list of donors [42e04d](https://github.com/panjf2000/gnet/commit/42e04ddc6ba2299ab89d107d2731725dacb31dcf)
- Add xs:code [33a3e5](https://github.com/panjf2000/gnet/commit/33a3e5744e0c3c6c3ac1d3d965eabc5c5a93307d)
- Build an official website for gnet [49363c](https://github.com/panjf2000/gnet/commit/49363cd41c73852bf52807a59b980ff2968f2e46)
- Move the majority of content to website and simplify READMEs [903ff8](https://github.com/panjf2000/gnet/commit/903ff8433d2d635f86b366b79eb304915c3fa662)

## Misc

- Fix a typo and improve code comments [37d75c](https://github.com/panjf2000/gnet/commit/37d75cce99609c9c17f25df2f6cfbfd3cf212ef8)
- Use SyscallError to wrap system calls errors [c3907e](https://github.com/panjf2000/gnet/commit/c3907e00e2bb725cc4616587fb2e52f1c74c5999)
- Refine `go build` tags of operating systems [637144](https://github.com/panjf2000/gnet/commit/637144b487d4fa4f0a3ef40a2dada40586e188b5)
- Format copyright info [6872c2](https://github.com/panjf2000/gnet/commit/6872c20ab7c97342512149843322ff6111003ec3)
- Remove all examples from the source-code repo [a704e0](https://github.com/panjf2000/gnet/commit/a704e0683e9c261c9f1e49c7443086068160fed2)
- refine the usage of os.NewSyscallError [2d54d8](https://github.com/panjf2000/gnet/commit/2d54d80f42a6fa8e293db63a35033e1daa60cd56)