+++
date = 2019-11-07
title = 'Gnet -- Support multiple codecs'
summary = 'Support multiple codecs to encode/decode TCP stream'
layout = 'page'
tags = ['Gnet', 'Open source', 'Release notes']
[params]
  author = 'Andy Pan'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

## About this change

In this change, gnet integrates multiple codecs to encode/decode network frames into/from TCP stream: LineBasedFrameCodec, DelimiterBasedFrameCodec, FixedLengthFrameCodec and LengthFieldBasedFrameCodec, referencing [netty codec](https://netty.io/4.1/api/io/netty/handler/codec/package-summary.html),
in addition to these built-in codecs, gnet also supports customized codecs by functional option `Codec`.