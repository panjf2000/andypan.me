+++
date = 2020-09-13
title = 'Gnet -- Support locking OS thread'
summary = 'Support locking each I/O event-loop goroutine to an OS thread'
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

Add functional option `LockOSThread` used to determine whether each I/O event-loop is associated to an OS thread, it is useful when you need some kind of mechanisms like thread local storage,
or invoke certain C libraries (such as graphics lib: GLib) that require thread-level manipulation via cgo, or want all I/O event-loops to actually run in parallel for a potential higher performance.
