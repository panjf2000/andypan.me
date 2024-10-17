---
title: TCP Keep-Alives 的原理和应用
date: 2024-08-05T18:52:29+08:00
params:
  author: Andy Pan
cover: /upload/half-open-connection-discovery.png
summary: 全面介绍 TCP Keep-Alive 在 Unix 和 Windows 平台上的原理和应用。
tags:
 - tcp
 - Kernel
 - 网络
 - 底层原理
isCJKLanguage: true
---

## 导言

今年初的时候我为 Go 语言实现了一个完整的 TCP Keep-Alives 机制[^1] [^2] [^3] [^4] [^5]。TCP keepalive 虽然是 TCP 协议标准[^6]却不是 POSIX 标准[^7]，然而用来配置 TCP keepalive 的三个套接字选项 `TCP_KEEPIDLE`、`TCP_KEEPINTVL` 和 `TCP_KEEPCNT` 却在类 Unix 操作系统和 Windows 上被广泛地支持，而 Go 作为一个跨平台的编程语言，其 TCP keepalive 的支持必须尽量覆盖所有提供了这三个套接字选项的操作系统：Linux、macOS、Windows、*BSD (FreeBSD/DragonflyBSD/OpenBSD/NetBSD) 以及 SunOS (Solaris/illumos)。

期间我深入调研了一下 TCP keepalive 在这些不同平台上的具体实现的异同，以及踩过不少坑，在把这个功能提交到 Go 语言的代码仓库之后，算是对这方面熟稔了一些，后来我还给 redis[^8]、libevent[^9] [^10]、libuv[^11] [^12] 和 curl[^13] [^14] [^15] 等一众知名的开源项目都实现/补充了 TCP keepalive 机制，现在通过本文分享一下相关的经验，希望能对那些需要编写跨平台程序的读者有所帮助。

## TCP Keep-Alives

我在上一篇文章《TCP 连接管理和数据传输全揭秘》[^16]那一章中已经介绍过 half-opened connections 的问题，正好可以用来引入 TCP keepalive 这个概念：

![](https://res.strikefreedom.top/static_res/blog/figures/half-open-connection-discovery.png)

具体流程文章中已经详细地描述过了，这里就不再赘述了，如果不记得的话请回到上一篇文章去复习一下。

A 端重启之后 A 和 B 之间的连接实际上就已经不能再用来实现可靠通信了。从 A 端的视角来看，这条连接已经不复存在了，然而从 B 端的视角来看，依旧是一切正常，因为 A 端的重启是一个突发的意外，连系统都整个宕机了所以 TCP 来不及通知 B 端终止这个连接，而系统对 TCP 连接也没有相应的持久化机制，因此在重启后也无法找回重启前所有的连接并一一通知远端关闭这些过期的连接，B 端当然就毫不知情了。这时候如果 A 端尝试重连或者 B 端尝试往对端发送数据的话，A 端的 TCP 就会检测到这是一条异常连接，通过发送 ***RST*** 报文到对端要求终止连接。

上面这种情况 TCP 能够妥善处理，但是现在考虑另一种可能的情况：A 端宕机之后就再也没有恢复过来了 (可能是硬件损坏导致机器彻底报废)，或者重启之后也不进行重连了，而 B 端正在等待 A 端发送完剩下的数据之后才能响应，也就是说两边都不会再去操作这条死连接 (dead connection) 了，这种现象被称之为连接泄露。如果系统中只有一两条死连接倒还好，影响不大，但要是系统管理的连接数巨大，而且其中有很多连接都因为对端异常退出而成为死连接的话就会很麻烦，因为这些死连接会持续占用系统的资源，从而挤占其他进程可用资源，如果系统资源比较紧张的话就会影响到整体的系统性能。TCP keepalive[^6] 正是为了解决这个问题而生的。

TCP keepalive 这一类的机制实际上属于 "防范于未然" 的机制，因为它预防的问题通常不会是那种发生概率很高的，也就是平时可能看起来没什么用，但是这一类问题往往引起的后果会很严重，也就是平时不发作，发作起来就要命了，而如果要做一个优秀的系统设计，那么就不能只考虑那些明显和高频的问题，那些隐蔽却后果严重的问题也要覆盖到，甚至于在某种程度上来说，这些隐蔽的问题才能真正体现一个系统架构师的真正水平。TCP keepalive 正是这样一个可以体现网络系统的健壮性的兜底机制。

### 定义

TCP 协议标准[^6]对空闲连接的定义是：如果在很长一段时间内既没有接收到数据也没有新的数据发送出去，则称 TCP 连接为 "空闲"。对 keepalive 的定义则是：为了确认空闲连接是否还 "活着"，TCP 会发送一个嗅探报文到对端去，看看是否有响应，这个报文的序列号通常是对端下一次发送数据应该使用的序列号过来减一，假设客户端和服务端三次握手建立连接之后就一直没有交换过数据，客户端的初始序列号是 0，那么客户端下一次发送数据报文时应该使用的序列号就是 1，而嗅探报文不应该消耗数据报文的序列号，因此就应该使用序列号 0  并且 ***ACK*** 值是自己上次发给对端的一样：***SQE=0,ACK=1***，这个嗅探报文可以包含一个字节的数据，也可以不包含数据 (之所以这样是因为早期有一些错误的 TCP 实现不支持这些包含倒退序列号且不带数据的报文，收到这一类的报文会忽略掉，所以这里是为了兼容性)。同时还规定了 TCP keepalive 必须可以在连接级别的粒度进行开关，且默认要关闭。

另外，嗅探报文的发送间隔时间是一个可配置的值，而且默认值必须不小于两个小时，而且每一个嗅探报文发送之前必须确保套接字缓冲区中没有已接收和待发送的数据。最后，TCP keepalive 机制不能仅凭对端没有回复自己的嗅探报文就断定该连接已死。

### 原理

TCP keepalive 的原理其实很简单，本质上就是一个针对空闲连接的定时器，假设 keepalive 的默认间隔时间是两小时，那么连接如果空闲达到两个小时，就会触发这个探活机制。探活的手段也很简单，TCP 自动给对端发送 keepalive probe，也就是一个保活嗅探报文。正如前文所述，这个报文的序列号会被设置成本端接收到的最新一个 ***ACK*** 值。这个嗅探报文发过去以后必定会发生以下三种情况之一：

1. 对端 TCP 回复一个 ***ACK***。上层用户程序无感知，因为一切正常。如果过后两小时连接上还是没有任何数据交换，那么 TCP 会再次发送一个保活嗅探报文。
2. 对端 TCP 回复一个 ***RST***。表示对端进程已崩溃或者重启，内核会关闭本端的 socket。上层用户程序中阻塞的系统调用 (`read()`/`recv`/`select()`/`poll()`/`epoll_wait()` 等) 会返回一个 `ECONNRESET` 或其他错误，告知用户程序对端连接已经异常断开。
3. 没有收到对端的任何响应。按照目前大多数源自 BSD 系统的 TCP 实现，通常会再发送 8 个或者 9 个嗅探报文，默认间隔时间为 75 秒。如果在这些嗅探报文发完之前能收到对端的回复，则还是判定该连接还存活，否则就放弃尝试关闭连接。

从历史上来讲，目前所有操作系统上的 socket 实现都是衍生自 BSD socket[^17]，而 BSD 对于 TCP keepalive 的支持是通过四个套接字选项：`SO_KEEPALIVE`、`TCP_KEEPIDLE`、`TCP_KEEPINTVL` 和 `TCP_KEEPCNT` 来实现的，虽然后三个选项不是 POSIX 标准，但是后来的大部分操作系统 (包括 Linux 等) 上的 TCP keepalive 也继承了这三个套接字选项。这里以 Linux 的官方文档为例来介绍一下这四个选项[^18] [^19]：

- `SO_KEEPALIVE`：开启或关闭 TCP keepalive 机制。默认关闭。
- `TCP_KEEPIDLE`：发送第一个保活嗅探报文之前 TCP 连接保持空闲的时长，也就是 TCP 标准里的嗅探报文的发送间隔时间。单位是秒，默认值是 2 小时。
- `TCP_KEEPCNT`：第一个保活嗅探报文没有收到对端响应之后，继续发送嗅探报文的次数。默认值是 9。
- `TCP_KEEPINTVL`：第一个保活嗅探报文没有收到对端响应之后，继续发送嗅探报文的间隔时间。单位是秒，默认值是 75 秒。

上面的是 socket 级别的参数，通过 `setsockopt()`[^20] 系统调用设置。

系统级别的内核参数如下：

```bash
  # cat /proc/sys/net/ipv4/tcp_keepalive_time
  7200
  # cat /proc/sys/net/ipv4/tcp_keepalive_intvl
  75
  # cat /proc/sys/net/ipv4/tcp_keepalive_probes
  9
```

通常不建议修改全局的 TCP keepalive 参数，因为影响面太广，而且也不是系统上的所有网络程序都适合同一种 keepalive 设置。

需要注意的是，上面这些的具体实现只需要本端有就够了，不需要对端也实现 keepalive，对端只需要支持普通的 TCP/IP 就能在本端开启 TCP keepalive 流程。这是因为 TCP 标准规定了如果连接的一端处于同步状态 (***ESTABLISHED***、***FIN-WAIT-1***、***FIN-WAIT-2***、***CLOSE-WAIT***、***CLOSING***、***LAST-ACK***、***TIME-WAIT***) 的话，收到对端的 ***ACK*** 报文如果发现其中的序列号不符合单调递增规则的话，需要回复另一个正确的 ***ACK*** 值来提示对端。TCP keepalive 正是利用了这一特性来实现保活嗅探的机制的，如此一来就不需要专门修改已有的 TCP 标准，是非常聪明的做法。

### 跨平台差异

如果网络系统要针对 socket 开启 TCP keepalive 的话，通常不会使用 `TCP_KEEPIDLE`、`TCP_KEEPINTVL` 和 `TCP_KEEPCNT`  的默认值。主要是因为 `TCP_KEEPIDLE` 的默认值是 2 小时，因此如果对端宕机或者重启了，另一端的程序要在超过两小时之后才收到通知，那就是说死连接要占用系统资源超过两个小时才会被释放掉，如果数量不多的话还能勉强接受，但要是数量巨大的话就会极大的浪费系统资源，此时这个 TCP keepalive 对于我们的系统来说就几乎没有什么用了。

正如 redis 的作者 [Salvatore Sanfilippo](https://github.com/antirez) 所说，TCP 标准定的这个默认值基本上就是个垃圾[^21]：

> Default settings are more or less garbage, with the keepalive time set to 7200 by default on Linux and other Unix-like systems. Modify settings to make the feature actually useful.

所以，大部分使用 TCP keepalive 的网络库和系统都会针对不同的操作系统 (平台) 修改默认值，使用更加合理的参数值。接下来我们来看看各大操作系统上的 TCP keepalive 有哪些异同和坑。

#### *BSD

正如前文所述，BSD 是最早实现了 socket 的操作系统，所以可以说 BSD socket 是目前所有其他操作系统上的 socket 实现的祖先，因此 BSD 系统上的 TCP keepalive 实现算是标准，BSD 目前的分支除了最初的 [FreeBSD](https://www.freebsd.org/) 之外，还有 [DragonFlyBSD](https://www.dragonflybsd.org/)、[OpenBSD](https://www.openbsd.org/) 和 [NetBSD](https://www.netbsd.org/)，值得一提的是在 BSD 平台上 `TCP_KEEPIDLE`、`TCP_KEEPINTVL` 和 `TCP_KEEPCNT` 这三个参数的默认值前两个和 Linux 一样，而最后一个默认值和 Linux 不同，Linux 上是 9，BSD 上是 8。

这里面还有一个坑：DragonFlyBSD 在 5.8 版本之前 `TCP_KEEPIDLE` 和 `TCP_KEEPINTVL` 这两个参数使用的时间单位是毫秒，而其他操作系统使用的一直是秒；5.8 版本之后开始改成秒以保持和其他平台的同步[^22] [^23]。

#### Linux

Linux 上实现的 TCP keepalive 已经在上一章中介绍过了，其实现继承自 BSD，所以没有太多惊喜。

#### macOS

早期的 macOS 只支持设置 `TCP_KEEPALIVE` (`TCP_KEEPIDLE` 在 macOS 上的别名)，但是从 [10.8 版本](https://en.wikipedia.org/wiki/OS_X_Mountain_Lion)[^24] [^25]开始支持 `TCP_KEEPINTVL` 和 `TCP_KEEPCNT`，macOS 10.8 发布也有十二年了，所以现在基本可以不用考虑版本兼容性，可以直接说 macOS 支持完整的 TCP keepalive 了。macOS 的代码是从 BSD 操作系统 fork 出来的，虽然经过这么多年的发展内核已经变动了很多，但很多特性还是兼容的，这三个参数的默认值也和 BSD 是一致的。

#### Windows

Windows 对 TCP keepalive 的支持很混乱。早期 (Windows 2000 开始) 是通过 [SIO_KEEPALIVE_VALS](https://learn.microsoft.com/en-us/windows/win32/winsock/sio-keepalive-vals) 控制码来支持，这个方式只支持设置 `TCP_KEEPIDLE` 和 `TCP_KEEPINTVL`，而且还只能两个一起设置，不能分开单独设置其中的一个，单位是毫秒，不支持 `TCP_KEEPCNT`。后来在 Windows 10 version 1703 这个版本中增加了 `TCP_KEEPCNT` 这个新的套接字选项[^26]，然后又在随后的 Windows 10 version 1709 版本中新增了 `TCP_KEEPIDLE` 和 `TCP_KEEPINTVL` 这两个套接字选项[^26]，保持和其他类 Unix 操作系统的同步，至此 Windows 操作系统就算是支持了完整的 TCP keepalive 机制。

#### SunOS

[Solaris](https://www.oracle.com/solaris/solaris11/) 和 [illumos](https://www.illumos.org/) 这两个基于 [SunOS](https://en.wikipedia.org/wiki/SunOS) 的操作系统上的 TCP keepalive 机制的混乱程度比之 Windows 是有过之而无不及。

这两个操作系统提供了两种方式来支持 TCP keepalive[^27] [^28] [^29]：

1. `TCP_KEEPALIVE_THRESHOLD` 和 `TCP_KEEPALIVE_ABORT_THRESHOLD`，单位是毫秒，前者默认值 2 小时，后者默认值是 8 分钟。
2. `TCP_KEEPIDLE`、`TCP_KEEPINTVL` 和 `TCP_KEEPCNT`，单位是秒，第一个默认值也是 2 小时，后两个没有默认值，必须手动设置。

先来说一说第一种，`TCP_KEEPALIVE_THRESHOLD` 这个套接字参数其实还比较好理解，因为它和 `TCP_KEEPIDLE` 是等价的，而 `TCP_KEEPALIVE_ABORT_THRESHOLD` 就比较复杂一点，首先它表示的是第一个保活嗅探报文发出去却没有收到响应之后，继续等待多长时间之后还没有回复就关闭连接，期间 TCP 会以[指数退避](https://en.wikipedia.org/wiki/Exponential_backoff) (而不是像其他操作系统那样使用等值时间间隔) 的方式发送嗅探报文。

第二种方式基本和其他操作系统上的三个 `TCP_KEEPIDLE`、`TCP_KEEPINTVL` 和 `TCP_KEEPCNT` 套接字选项相同。

需要注意的是，illumos 在从 [OpenSolaris](https://en.wikipedia.org/wiki/OpenSolaris) 项目 fork 代码出来之后的第二年 (2011 年) 就实现了第二种方式[^30]，所以它一直都支持这两种 TCP keepalive 的方式；而 Solaris 则是要到 2018 年的 Solaris 11.4 才支持第二种方式，之前一直都是用的第一种。

这里有两个坑：首先是 `TCP_KEEPALIVE_THRESHOLD`，这个套接字选项的最小值是 10 秒，最大值是 10 天，超过这个范围 `setsockopt()` 会直接报错，这一点 solaris 和 illumos 是一样的。但是第二种方式中的 `TCP_KEEPIDLE` 的值在两个系统上的取值范围是不一致的，在 solaris 上这个参数的取值范围和 `TCP_KEEPALIVE_THRESHOLD` 是一样的，而在 illumos 上的取值范围更加大 (但是在 man pages 中又没有写...)，所以如果你用 `setsockopt()` 在 solaris 设置 `TCP_KEEPIDLE` 小于 10 秒或者大于 10 天就会报错，但是在 illumos 这么做就不会有问题，而这个行为在 solaris 的文档中并没有写清楚[^29]，当时我在实现 Go 的 TCP keepalive 时使用了小于 10 秒的测试用例，于是测试代码在其他操作系统上都是一直成功的，只有在 solaris 上一直失败，让我百思不得其解，又因为 solaris 是闭源的操作系统所以看不到内核的源码，网上关于 solaris 的资料也太少，困扰了我很久，最后还是在看 illumos 的源码时发现了[^31]这部分有做取值范围检查：

```c
		/*
		 * TCP_KEEPIDLE is in seconds but TCP_KEEPALIVE_THRESHOLD
		 * is in milliseconds. TCP_KEEPIDLE is introduced for
		 * compatibility with other Unix flavors.
		 * We can fall through TCP_KEEPALIVE_THRESHOLD logic after
		 * converting the input to milliseconds.
		 */
		case TCP_KEEPIDLE:
			*i1 *= 1000;
			/* FALLTHRU */

		case TCP_KEEPALIVE_THRESHOLD:
			if (checkonly)
				break;

			if (*i1 < tcps->tcps_keepalive_interval_low ||
			    *i1 > tcps->tcps_keepalive_interval_high) {
				*outlenp = 0;
				return (EINVAL);
			}
```

最后猜测 solaris 可能有类似的实现，一经测试，果然如此，最终才解决了这个问题。

第二个更大的坑是，solaris 和 illumos 操作系统上 `TCP_KEEPINTVL` 和 `TCP_KEEPCNT` 这两个套接字选项是配套使用的，也就是说如果只设置了其中一个的话，就会用 `TCP_KEEPALIVE_ABORT_THRESHOLD` 的默认值 (8 分钟，480 秒) 除以那个已设置的参数值，然后算出结果作为另一个没有设置的参数的值。比如，如果只设置了 `TCP_KEEPCNT` 的值为 10 但是没有设置 `TCP_KEEPINTVL`，最后系统就会自动计算 `TCP_KEEPINTVL` = `TCP_KEEPALIVE_ABORT_THRESHOLD`/`TCP_KEEPCNT`，也就是 48s = 480s/10，然后将这个值写入 socket 的 TCP keepalive 设置[^32]：

```c
		/*
		 * tcp_ka_abort_thres = tcp_ka_rinterval * tcp_ka_cnt.
		 * So setting TCP_KEEPCNT or TCP_KEEPINTVL can affect all the
		 * three members - tcp_ka_abort_thres, tcp_ka_rinterval and
		 * tcp_ka_cnt.
		 */
		case TCP_KEEPCNT:
			if (checkonly)
				break;

			if (*i1 == 0) {
				return (EINVAL);
			} else if (tcp->tcp_ka_rinterval == 0) {
				/*
				 * When TCP_KEEPCNT is specified without first
				 * specifying a TCP_KEEPINTVL, we infer an
				 * interval based on a tunable specific to our
				 * stack: the tcp_keepalive_abort_interval.
				 * (Or the TCP_KEEPALIVE_ABORT_THRESHOLD, in
				 * the unlikely event that that has been set.)
				 * Given the abort interval's default value of
				 * 480 seconds, low TCP_KEEPCNT values can
				 * result in intervals that exceed the default
				 * maximum RTO of 60 seconds.  Rather than
				 * fail in these cases, we (implicitly) clamp
				 * the interval at the maximum RTO; if the
				 * TCP_KEEPCNT is shortly followed by a
				 * TCP_KEEPINTVL (as we expect), the abort
				 * threshold will be recalculated correctly --
				 * and if a TCP_KEEPINTVL is not forthcoming,
				 * keep-alive will at least operate reasonably
				 * given the underconfigured state.
				 */
				uint32_t interval;

				interval = tcp->tcp_ka_abort_thres / *i1;

				if (interval < tcp->tcp_rto_min)
					interval = tcp->tcp_rto_min;

				if (interval > tcp->tcp_rto_max)
					interval = tcp->tcp_rto_max;

				tcp->tcp_ka_rinterval = interval;
			} else {
				if ((*i1 * tcp->tcp_ka_rinterval) <
				    tcps->tcps_keepalive_abort_interval_low ||
				    (*i1 * tcp->tcp_ka_rinterval) >
				    tcps->tcps_keepalive_abort_interval_high)
					return (EINVAL);
				tcp->tcp_ka_abort_thres =
				    (*i1 * tcp->tcp_ka_rinterval);
			}
			tcp->tcp_ka_cnt = *i1;
			break;
		case TCP_KEEPINTVL:
			/*
			 * TCP_KEEPINTVL is specified in seconds, but
			 * tcp_ka_rinterval is in milliseconds.
			 */

			if (checkonly)
				break;

			if ((*i1 * 1000) < tcp->tcp_rto_min ||
			    (*i1 * 1000) > tcp->tcp_rto_max)
				return (EINVAL);

			if (tcp->tcp_ka_cnt == 0) {
				tcp->tcp_ka_cnt =
				    tcp->tcp_ka_abort_thres / (*i1 * 1000);
			} else {
				if ((*i1 * tcp->tcp_ka_cnt * 1000) <
				    tcps->tcps_keepalive_abort_interval_low ||
				    (*i1 * tcp->tcp_ka_cnt * 1000) >
				    tcps->tcps_keepalive_abort_interval_high)
					return (EINVAL);
				tcp->tcp_ka_abort_thres =
				    (*i1 * tcp->tcp_ka_cnt * 1000);
			}
			tcp->tcp_ka_rinterval = *i1 * 1000;
			break;
```

这个源码是 illumos 的，但我估计 solaris 是直接抄过去用了，因为 solaris 在这里的表现和 illumos 是一致的。这个更加坑人，我当时写的测试用例也因为这个坑而一直失败，迟迟不能把代码合入 Go 的主线，过了很久我才从 illumos 的源码中定位到这个问题，关键是不管是 solaris[^29] 还是 illumos[^27] 的官方文档都没有提到过这一点，实在是太坑了。。。

#### IBM AIX

IBM 的 AIX 操作系统同样支持完整的 TCP keepalive[^33]。不过要注意的是虽然 AIX 的三个 socket 级别的套接字选项使用时间单位和其他系统一样都是秒，但是其系统全局对应的三个参数使用的时间单位却是半秒。`TCP_KEEPIDLE` 和 `TCP_KEEPINTVL` 的默认值和 Linux 是一样的，`TCP_KEEPCNT` 在一些早期的版本中的默认值是 8 或者 9。

#### 其他

还有其他的诸如 HP-UX 等其他的操作系统也都支持 `TCP_KEEPIDLE`、`TCP_KEEPINTVL` 和 `TCP_KEEPCNT`[^34]，不过因为都是一些比较小众的操作系统，这里就不一一介绍了，有兴趣的读者可以自行查阅。

## 总结

本文剖析了 TCP keepalive 机制的基本原理和它在不同的操作系统上的异同，以及很多笔者亲自踩过的跨平台的坑。作为经验分享出来，希望能对以后各位编写跨平台程序时有所启发和借鉴。

## 参考 & 延伸阅读

- [RFC 9293 —— Transmission Control Protocol (TCP)](https://datatracker.ietf.org/doc/html/rfc9293)
- [The Sockets Networking API: UNIX® Network Programming Volume 1, Third Edition](https://www.oreilly.com/library/view/the-sockets-networking/0131411551/)
- [Transmission Control Protocol —— Wikipedia](https://en.wikipedia.org/wiki/Transmission_Control_Protocol)

[^1]: [net: add KeepAliveConfig and implement SetKeepAliveConfig](https://github.com/golang/go/commit/d42cd452dcca76819dd385a7775f8453d6255dbd)
[^2]: [net: harmonize the time units used for TCP keep-alive on DragonFly and other UNIX's by seconds](https://github.com/golang/go/commit/d4112310a4d3b1981d77226a3d52a8b566b0c0bc)
[^3]: [net: support TCP_KEEPIDLE, TCP_KEEPINTVL and TCP_KEEPCNT on newer Windows](https://github.com/golang/go/commit/1cce1a6a1110a53c1aa8fa0f40b69307ff641ca4)
[^4]: [net: bifurcate the TCP Keep-Alive mechanism into Solaris and illumos](https://github.com/golang/go/commit/16df5330e410cfc702d942eb0cf3707ccdfd2c1d)
[^5]: [net: implement TCP_KEEPIDLE, TCP_KEEPINTVL, and TCP_KEEPCNT on Solaris 11.4](https://github.com/golang/go/commit/91c04826723a10f6778a935e743a34de81312489)
[^6]: [RFC 9293 —— TCP Keep-Alives](https://datatracker.ietf.org/doc/html/rfc9293#name-tcp-keep-alives)
[^7]: [The Open Group Base Specifications Issue 7, 2018 edition](https://www.opengroup.org/onlinepubs/9699919799/)
[^8]: [Implement TCP Keep-Alives across most Unix-like systems](https://github.com/redis/redis/pull/12782)
[^9]: [Implement full support of TCP Keep-Alives across most Unix-like OS's](https://github.com/libevent/libevent/pull/1532)
[^10]: [Enable the full TCP KeepAlive mechanism on Windows](https://github.com/libevent/libevent/pull/1568)
[^11]: [unix: support full TCP keep-alive on Solaris](https://github.com/libuv/libuv/pull/4272)
[^12]: [tcpkeepalive: distinguish OS versions and use proper time units](https://github.com/libuv/libuv/pull/4428)
[^13]: [socket: change TCP keepalive from ms to seconds on DragonFly BSD](https://github.com/curl/curl/commit/6da320357f14251ee8f82d3fd6632148bcecaadf)
[^14]: [tcpkeepalive: support setting TCP keep-alive parameters on Solaris <11.4](https://github.com/curl/curl/commit/f51fa8f169f66792104cdea984b7243286116dea)
[^15]: [tcpkeepalive: add CURLOPT_TCP_KEEPCNT and --keepalive-cnt](https://github.com/curl/curl/commit/b77d627d2425650d1ad7aa5c83c45ec7f542ec60)
[^16]: [TCP 连接管理和数据传输全揭秘](https://strikefreedom.top/archives/tcp-connection-management-and-data-transmission-in-depth)
[^17]: [Berkeley sockets —— Wikipedia](https://en.wikipedia.org/wiki/Berkeley_sockets)
[^18]: [socket(7) — Linux manual page](https://man7.org/linux/man-pages/man7/socket.7.html)
[^19]: [tcp(7) — Linux manual page](https://man7.org/linux/man-pages/man7/tcp.7.html)
[^20]: [setsockopt(3p) — Linux manual page](https://man7.org/linux/man-pages/man3/setsockopt.3p.html)
[^21]: [redis —— src/anet.c](https://github.com/redis/redis/blob/e4ddc344635315a494b8a05a5af1c799c44a8f9a/src/anet.c#L212-L214)
[^22]: [DragonFly BSD 5.8 release](https://www.dragonflybsd.org/release58/)
[^23]: [git: DragonFly_RELEASE_5_4 kernel - Change tcp keepalive options from ms to seconds (DISRUPTIVE)** **](https://lists.dragonflybsd.org/pipermail/commits/2019-July/719125.html)
[^24]: [Re: TCP_KEEPIDLE, TCP_KEEPINTVL and FTP](https://lists.apple.com/archives/macnetworkprog/2012/Jul/msg00005.html)
[^25]: [darwin-xnu —— bsd/netinet/tcp.h](https://github.com/apple/darwin-xnu/blob/main/bsd/netinet/tcp.h#L215-L230)
[^26]: [IPPROTO_TCP socket options](https://learn.microsoft.com/en-us/windows/win32/winsock/ipproto-tcp-socket-options)
[^27]: [**tcp**, **TCP** - Internet Transmission Control Protocol ](https://illumos.org/man/4P/tcp)
[^28]: [Solaris 11.3 —— tcp(7P)](https://docs.oracle.com/cd/E86824_01/html/E54777/tcp-7p.html)
[^29]: [Solaris 11.4 —— tcp(4P)](https://docs.oracle.com/cd/E88353_01/html/E37851/tcp-4p.html)
[^30]: [1361 Add support for socket options TCP_KEEPCNT, TCP_KEEPIDLE, TCP_KEEPINTVL](https://github.com/illumos/illumos-gate/commit/3d0a255c417cf2e7b69e770de43f195b0eeffacb)
[^31]: [illumos-gate —— /usr/src/uts/common/inet/tcp/tcp_opt_data.c#L767-L771](https://github.com/illumos/illumos-gate/blob/6119f23667e479c4408c609cf002d80c45d05c3c/usr/src/uts/common/inet/tcp/tcp_opt_data.c#L767-L771)
[^32]: [illumos-gate —— /usr/src/uts/common/inet/tcp/tcp_opt_data.c#L790-L871](https://github.com/illumos/illumos-gate/blob/6119f23667e479c4408c609cf002d80c45d05c3c/usr/src/uts/common/inet/tcp/tcp_opt_data.c#L790-L871)
[^33]: [IBM AIX 6.4.0 —— TCP/IP settings](https://www.ibm.com/docs/en/sdse/6.4.0?topic=planning-tcpip-settings)
[^34]: [HP-UX —— TCP(7P)](https://docstore.mik.ua/manuals/hp-ux/en/B2355-60130/TCP.7P.html)