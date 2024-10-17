---
title: 一个优秀的开源项目之死
date: 2022-01-01
params:
  author: Andy Pan
summary: gogo-protobuf 即将寿终正寝，这是一个故事。
tags:
 - 个人
 - 随笔
showWordCount: true
---

## 陨落

相信很多 Go 语言开发者应该都对 [gogo-protobuf](https://github.com/gogo/protobuf) 这个第三方开源的 protobuffer 库并不陌生，目前有大量的线上项目使用了它，使用者中不乏 [k8s](https://github.com/kubernetes/kubernetes)、[etcd](https://github.com/etcd-io/etcd)、[tidb](https://github.com/pingcap/tidb)、[dgraph](https://github.com/dgraph-io/dgraph) 等顶级开源项目，甚至商业公司如 dropbox 等也在列，此外，我想国内很多大中小厂应该也有在线上使用：

![](https://res.strikefreedom.top/static_res/blog/figures/st1640960975.PNG)

gogo-protobuf 是官方的 [golang/protobuf](https://github.com/golang/protobuf) 的一个 fork，却青出于蓝而胜于蓝，其性能相较于官方库有了飞跃般的提升，相关细节可以访问 [gogo-protobuf benchmarks](https://github.com/gogo/protobuf/blob/master/bench.md)。其突出的性能也正是它能受到各大开源项目青睐的原因。

**然而，这个开源项目即将寿终正寝了**。

最近又逛到了 gogo-protobuf 的主页，看到维护团队两年前发的一条寻求外部公司/组织帮助接手该项目的公告，依旧没有站出来接盘的公司/组织；我还是难以想象一个如此应用广泛、性能远超官方的的开源库，发展到现在，不是欣欣向荣，反而是陷入了难以为继的局面：

![](https://res.strikefreedom.top/static_res/blog/figures/st1640960004.PNG)

于是，前几天我给作者发了一封邮件，表达了希望捐赠的想法，略尽绵薄之力，昨天收到了他的回复邮件说他不能接受捐赠，他说能听到使用者对他们工作的认同就很满足了，他也说了，随着 [protobuf-apiv2](https://blog.golang.org/protobuf-apiv2) 的发布，gogo-protobuf 已经无法兼容官方的标准了，而这个项目的升级需要先完成这些兼容性的开发，但这是一项巨大的工程，需要花费大量的人力，除非有一个全职的团队来继续开发迭代这个项目，否则项目将在未来被废弃掉，很不幸的是他们都有自己的本职工作，无法继续花费大量的时间精力在 gogo-protobuf 上了，虽然很遗憾，但是他们不得不最终放弃这个项目了：

![](https://res.strikefreedom.top/static_res/blog/figures/st1640970709.jpg)

## 开源

50 年前，伟大的 **Richard Matthew Stallman** 发起并领导了轰轰烈烈的 [GNU](https://www.gnu.org/) 项目，并亲自开发了 [GCC](https://en.wikipedia.org/wiki/GNU_Compiler_Collection)、[GDB](https://en.wikipedia.org/wiki/GNU_Debugger)、[Emacs](https://en.wikipedia.org/wiki/Emacs) 这些我们现在耳熟能详的软件。后来，一个芬兰大学生 Linus 因为他们学校教学用的 [MINIX](https://en.wikipedia.org/wiki/Minix) 操作系统不被允许在教学外使用而心生不满，于是自己动手在 MINIX 上开发起了自己的操作系统，并在第一版内核能跑起来之后，使用 GNU GPL 协议在网上发布了他的第一版内核源码，命名 [Linux](https://en.wikipedia.org/wiki/Linux)，后来陆陆续续有开发者加入该内核的开发，借助 GNU 开源运动的东风，Linux 犹如大鹏同风起，扶摇直上九万里，最终填补了 GNU 计划中缺失的内核这一重要环节，便有了 **GNU/Linux**。Linux 也成为了世界上最重要的开源操作系统。GNU 运动中诞生了大量伟大的开源作品： Linux、GCC、GDB、Emacs、PHP、MySQL 等等。

开源，虽然被很多人称之为人类创造的最伟大的概念之一，计算机领域也因为开源运动的兴起而在过去的几十年间蓬勃发展，开源软件虽然表面看似风光无限，但是实际上也会有步履维艰的时候。前阵子闹得沸沸扬扬的 Log4j 漏洞事件，影响了全球成千上万的项目和公司，其实是揭开了开源软件的一块遮羞布：目前依然有大量优秀的、应用广泛的第三方开源库仅仅由少量的开发者在维护，得不到足够的资金支持。

然而讽刺的是，偏偏有很多这一类小作坊的开源软件却被全世界大量的商业公司应用在生产环境上，每年攫取数以十亿计的利润，而这些优秀的开源项目的作者却没有从中得到一毛钱的回报，也很少有商业公司在享受了开源软件为自己带来了巨额营收之后会去回馈作者或者社区，甚至有很多大型商业公司心安理得地白嫖优秀的开源项目，这几年 Redis 和 Elasticsearch 等开源软件都更换了自己开源证书，就是不满被云服务商白嫖，前阵子我还专门发朋友圈吐槽过这个事情：

![](https://res.strikefreedom.top/static_res/blog/figures/IMG_4780.jpg)

有兴趣的读者可以浏览[原文](https://mp.weixin.qq.com/s/jv_FpFY0bnf12ZLtE1BTBA)。

事实上，世界上有不少开源软件成功商业化且能够持续发展的例子（或者反过来，商业公司做开源）：比如 Kafka 之于 [Confluent 公司](https://www.confluent.io/)，Redis 之于 [Redis Labs 公司](https://redis.com/)，ElasticSearch 之于 [Elastic 公司](https://www.elastic.co/)，TiDB 之于 [PingCap 公司](https://pingcap.com/)，等等；还有其他虽然没有成立大型商业公司但是本身运营也很成功的开源项目，作者每年有稳定且高额的开源捐赠收入，也能保证项目的可持续发展，如 Vue.js。

但是，并非所有广受欢迎的开源软件都能乘势而上成功商业化，为作者提供高额的回报。现实情况更多是开源软件长期得不到有效的支持和回报，作者慢慢失去了继续开发维护的动力，因此渐渐被废弃，只留下一地鸡毛。

## 有感

Walter 在给我的回复邮件中表达了对 gogo-prorobuf 这个开源项目即将死去的深深的无奈和无力，我相信每一个开源软件作者对自己的作品肯定是极其珍视的，正如他邮件里所说，能够得到来自全世界使用者的认同是一种美好的感受，我相信他们并不强求资金回报，也因此 Walter 婉拒了我的捐赠，高风亮节。我自己也是一个开源项目的作者，这里我就不给自己打广告了，只是想分享一下自己业余搞开源这两三年的一些感想：

> 曾经因为我的开源库在别人的线上环境出 bug 而被私信问候；曾经有人接入了我的开源库遇到解决不了的问题，我用自己的周末时间帮他调试定位并最终解决，然而在别人眼里仿佛是一件理所应当的事情，事了拂衣去；曾经有公司在线上重度使用了我的开源库，我询问能否将其添加到项目首页作为用户案例，帮我做个小小的宣传，被拒绝，说是暂时不想公开公司产品背后的技术栈，等等。

幸而，这期间也不全是此类愁云惨淡的故事，也曾收到很多致谢邮件，国内的、国外的；也不乏一些慷(有)慨(钱)之(任)士(性)，话不多说，直接打钱；甚至还有匿名捐赠的用户，我请他们留个名字也被婉拒。正是这些可爱的人和事，让我对开源的信念从未减淡，而且，我本人也很希望自己创作的开源作品能够被更多的人、更多的项目、更多的公司使用，人生有很多方式去实现自我价值，我认为这是其中的一种。

希望以后能有更多的商业公司能够回馈开源社区，支持开源作者，不一定是要资金上的支持，我相信形式是多种多样的，持续探索。

谢谢！

