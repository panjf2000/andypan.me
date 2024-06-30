+++
date = 2020-03-31
title = 'Gnet -- Support new load-balancing algorithm'
summary = 'Support new load-balancing algorithm of least-connections'
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

In the past, gnet has only one load-balancing algorithm: Round-Robin, now it supports a new one: Least-Connections
and provide the new functional option `LB`, enabling users to switch load-balancing algorithm at their own sweet will.

