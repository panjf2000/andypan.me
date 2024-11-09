
+++
date = 2024-11-09
title = 'Release of gnet v2.6.0'
summary = 'Release of the official stable version of v2.6.0'
layout = 'page'
tags = ['Gnet', 'Open source', 'Release notes', 'Announcement']
[params]
  author = 'Andy Pan'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

I hereby announce the release of [gnet v2.6.0](https://github.com/panjf2000/gnet/releases/tag/v2.6.0), where we've added two major features of [`SO_BINDTODEVICE`](https://man7.org/linux/man-pages/man7/socket.7.html) and [configurable edge-triggered chunk](https://github.com/panjf2000/gnet/pull/646), along with a few bug-fixes.

Another change to note is that starting with this release, the minimum required Go version to run `gnet` is 1.20!