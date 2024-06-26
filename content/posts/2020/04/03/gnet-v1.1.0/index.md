+++
date = 2020-04-03
title = 'Released gnet v1.1.0'
summary = 'Released the official stable version of v1.1.0'
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

- Support new load-balancing algorithm of least-connections [#63](https://github.com/panjf2000/gnet/pull/63)
- Support new load-balancing algorithm of source-addr-hash [#64](https://github.com/panjf2000/gnet/pull/64)
- Add a new API to get the number of currently active connections [5ae4849](https://github.com/panjf2000/gnet/commit/5ae4849ac2941394b21254950ec7101081add782)

## Bugfixes

- Fix an issue of not recycling byte-buffers [3b6edfd](https://github.com/panjf2000/gnet/commit/3b6edfdd933abab07a823bc0760c3d24b61b0879)
- Fix a bug of reading data from the full ring-buffer [a9caa26](https://github.com/panjf2000/gnet/commit/a9caa26689f9ecb46a98feb3bb5513639c8beb98)

## Enhancements

- Improve several methods of ring-buffer [828e845](https://github.com/panjf2000/gnet/commit/828e845558c49918a647a1144eab8d9ca35887de)
- Refine the range indexes when re-slicing [008c0c9](https://github.com/panjf2000/gnet/commit/008c0c9e4c702431db6b3d4372be94ea99ac5a5e)
- Lazily allocate memory for ring-buffers [095ef52](https://github.com/panjf2000/gnet/commit/095ef52c9275b5382b7e557da7bb40c5a3b156ca)

## Docs

- Switch go doc to pkg.go.dev [b973741](https://github.com/panjf2000/gnet/commit/b973741b7415e2e42b16e8dcf4adf6553c41ed40)
- Add TechEmpower benchmarks [65d859b](https://github.com/panjf2000/gnet/commit/65d859b02a7766808151db5a809f8776ba708cfd) [9bd48a3](https://github.com/panjf2000/gnet/commit/9bd48a348281542d3cac008b5525a693981ec525) [04b473e](https://github.com/panjf2000/gnet/commit/04b473e20f51afe4b798d54b336fc72a7c5cd7b0) [3743d0f](https://github.com/panjf2000/gnet/commit/3743d0f26ff38fec6872be0d13eac7e7d370d4b9)
- Improve some statements [cc4ccd4](https://github.com/panjf2000/gnet/commit/cc4ccd470b0a4fabf902896df714b7fd07346216)

## Misc

- Rename the eventloop_group.go to load_balancing.go [24203f4](https://github.com/panjf2000/gnet/commit/24203f4d936374666ef73c1ff7d96656c4de5fcb)
- Add unit test for ring-buffer ecf3473072d1c02ca69b469d24c5581eae041d2f [94246dc](https://github.com/panjf2000/gnet/commit/94246dc0fa7c0fcd02e0498e2a355f661b1403c8)