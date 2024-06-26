+++
date = 2020-05-11
title = 'Released gnet v1.2.0'
summary = 'Released the official stable version of v1.2.0'
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

- Support graceful shutdown with OS signals (Fixes #82)

## Enhancements

- Improve the creation processes of netpoller (Fixes #72) [ccc6c25](https://github.com/panjf2000/gnet/commit/ccc6c25923404195e7fba960d6cdfb57d3a7cea4)
- Reuse slice struct when resetting buffer [916c255](https://github.com/panjf2000/gnet/commit/916c255de373b3ff73eaa10cb1f0bacc2c1b6e3f)
- Make a big allocation on ring-buffer at the first time [61963bd](https://github.com/panjf2000/gnet/commit/61963bd439beba73ef29c74b9870442c867e192a)

## Docs

- Update `Features` section in READMEs [a0d53f5](https://github.com/panjf2000/gnet/commit/a0d53f5061405901e17df4fa9760633edd9f1d98)
- Add a new article about gnet [d500bf4](https://github.com/panjf2000/gnet/commit/d500bf449f7808df210ff859014387a833c2f9f2)
- Supplement the missing acknowledgment item [b031357](https://github.com/panjf2000/gnet/commit/b031357ed623f630048a9222229e76c05cb7e6ef)

## Misc

- Enable ReadN(int) to handle corner cases [2e89e25](https://github.com/panjf2000/gnet/commit/2e89e255fd636371c0671542387e807bcd18a496)
- Leverage reviewdog as automated code review tool [5db42f9](https://github.com/panjf2000/gnet/commit/5db42f9413a7f17b6e6d8383f0e9c7c6dbf3a9d7)
- Use customized logger to log errors instead of std log package [3a69701](https://github.com/panjf2000/gnet/commit/3a697014af6c6a61828147d2def1f7a543e149b6)
- Remove useless field of server [47817a0](https://github.com/panjf2000/gnet/commit/47817a0b7098443d141e8857cbfc4d30f13b3965)
- Unexport some errors [5287bd8](https://github.com/panjf2000/gnet/commit/5287bd8bde31bbcdfc482810479cd6c773afdeeb)