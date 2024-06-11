+++
date = '2020-07-02'
title = 'Gnet -- New approach to create server sockets'
summary = 'Leverage system calls instead of `net` package to create sockets'
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

Benefit from system calls provided by Go standard library, server listener sockets inside gnet have gotten rid of the Go `net` package eventually,
there are no more methods from `net` package involved in the process of creating the listener of server socket, all by raw system calls.
