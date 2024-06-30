+++
date = 2018-05-19
title = 'Goroutine pool -- ants'
summary = '🐜🐜🐜 ants is a high-performance and low-cost goroutine pool in Go.'
layout = 'page'
tags = ['Ants', 'Go', 'Open source', 'Announcement']
[params]
  author = 'Andy Pan'
showAuthor = true
showDate = true
showReadingTime = true
showWordCount = true
+++

## 📖 Introduction

### GitHub

https://github.com/panjf2000/ants

Library `ants` implements a goroutine pool with fixed capacity, managing and recycling a massive number of goroutines, allowing developers to limit the number of goroutines in your concurrent programs.

## 🚀 Features:

- Managing and recycling a massive number of goroutines automatically
- Purging overdue goroutines periodically
- Abundant APIs: submitting tasks, getting the number of running goroutines, tuning the capacity of the pool dynamically, releasing the pool, rebooting the pool
- Handle panic gracefully to prevent programs from crash
- Efficient in memory usage and it even achieves [higher performance](#-performance-summary) than unlimited goroutines in Golang
- Nonblocking mechanism

## 💡 How `ants` works

### Flow Diagram

<p align="center">
<img width="1011" alt="ants-flowchart-en" src="https://user-images.githubusercontent.com/7496278/66396509-7b42e700-ea0c-11e9-8612-b71a4b734683.png">
</p>

### Activity Diagrams

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/go/ants-pool-1.png)

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/go/ants-pool-2.png)

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/go/ants-pool-3.png)

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/go/ants-pool-4.png)

## 🖥 Use cases

### business companies

The following companies/organizations use `ants` in production.

<table>
  <tbody>
    <tr></tr>
    <tr>
      <td align="center" valign="middle">
        <a href="https://www.tencent.com">
          <img src="https://res.strikefreedom.top/static_res/logos/tencent_logo.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.bytedance.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/ByteDance_Logo.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://tieba.baidu.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/baidu-tieba-logo.png" width="300" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://weibo.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/weibo-logo.png" width="300" />
        </a>
      </td>
    </tr>
    <tr>
      <td align="center" valign="middle">
        <a href="https://www.tencentmusic.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/tencent-music-logo.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.futuhk.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/futu-logo.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.shopify.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/shopify-logo.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.wechat.com/en/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/wechat-logo.png" width="250" />
        </a>
      </td>
    </tr>
    <tr>
      <td align="center" valign="middle">
        <a href="https://www.baidu.com/" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/baidu-mobile.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.360.com" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/360-logo.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.huaweicloud.com/intl/en-us/" target="_blank">
          <img src="https://res-static.hc-cdn.cn/cloudbu-site/china/zh-cn/%E7%BB%84%E4%BB%B6%E9%AA%8C%E8%AF%81/pep-common-header/logo-en.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.matrixorigin.io" target="_blank">
          <img src="https://www.matrixorigin.io/_next/static/media/logo-light-en.42553c69.svg" width="250" />
        </a>
      </td>
    </tr>
    <tr>
      <td align="center" valign="middle">
        <a href="https://adguard-dns.io" target="_blank">
          <img src="https://cdn.adtidy.org/website/images/AdGuardDNS_black.svg" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://bk.tencent.com" target="_blank">
          <img src="https://static.apiseven.com/2022/11/14/6371adab14119.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.alibabacloud.com" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/aliyun-intl.png" width="250" />
        </a>
      </td>
      <td align="center" valign="middle">
        <a href="https://www.zuoyebang.com" target="_blank">
          <img src="https://res.strikefreedom.top/static_res/logos/zuoyebang-logo.jpeg" width="300" />
        </a>
      </td>
    </tr>
    <tr>
      <td align="center" valign="middle">
        <a href="https://www.antgroup.com/en" target="_blank">
          <img src="https://gw.alipayobjects.com/mdn/rms_27e257/afts/img/A*PLZaSZnCPAwAAAAAAAAAAAAAARQnAQ" width="250" />
        </a>
      </td>
    </tr>
    <tr></tr>
  </tbody>
</table>

### open-source software

The open-source projects below do concurrent programming with the help of `ants`.

- [gnet](https://github.com/panjf2000/gnet):  A high-performance, lightweight, non-blocking, event-driven networking framework written in pure Go.
- [milvus](https://github.com/milvus-io/milvus): An open-source vector database for scalable similarity search and AI applications.
- [nps](https://github.com/ehang-io/nps): A lightweight, high-performance, powerful intranet penetration proxy server, with a powerful web management terminal.
- [siyuan](https://github.com/siyuan-note/siyuan): SiYuan is a local-first personal knowledge management system that supports complete offline use, as well as end-to-end encrypted synchronization.
- [osmedeus](https://github.com/j3ssie/osmedeus): A Workflow Engine for Offensive Security.
- [jitsu](https://github.com/jitsucom/jitsu/tree/master): An open-source Segment alternative. Fully-scriptable data ingestion engine for modern data teams. Set-up a real-time data pipeline in minutes, not days.
- [triangula](https://github.com/RH12503/triangula): Generate high-quality triangulated and polygonal art from images.
- [teler](https://github.com/kitabisa/teler): Real-time HTTP Intrusion Detection.
- [bsc](https://github.com/binance-chain/bsc): A Binance Smart Chain client based on the go-ethereum fork.
- [jaeles](https://github.com/jaeles-project/jaeles): The Swiss Army knife for automated Web Application Testing.
- [devlake](https://github.com/apache/incubator-devlake): The open-source dev data platform & dashboard for your DevOps tools.
- [matrixone](https://github.com/matrixorigin/matrixone): MatrixOne is a future-oriented hyper-converged cloud and edge native DBMS that supports transactional, analytical, and streaming workloads with a simplified and distributed database engine, across multiple data centers, clouds, edges and other heterogeneous infrastructures.
- [bk-bcs](https://github.com/TencentBlueKing/bk-bcs): BlueKing Container Service (BCS, same below) is a container management and orchestration platform for the micro-services under the BlueKing ecosystem.
- [trueblocks-core](https://github.com/TrueBlocks/trueblocks-core): TrueBlocks improves access to blockchain data for any EVM-compatible chain (particularly Ethereum mainnet) while remaining entirely local.
- [openGemini](https://github.com/openGemini/openGemini): openGemini is an open-source,cloud-native time-series database(TSDB) that can be widely used in IoT, Internet of Vehicles(IoV), O&M monitoring, and industrial Internet scenarios.
- [AdGuardDNS](https://github.com/AdguardTeam/AdGuardDNS): AdGuard DNS is an alternative solution for tracker blocking, privacy protection, and parental control.
- [WatchAD2.0](https://github.com/Qihoo360/WatchAD2.0): WatchAD2.0 是 360 信息安全中心开发的一款针对域安全的日志分析与监控系统，它可以收集所有域控上的事件日志、网络流量，通过特征匹配、协议分析、历史行为、敏感操作和蜜罐账户等方式来检测各种已知与未知威胁，功能覆盖了大部分目前的常见内网域渗透手法。
- [vanus](https://github.com/vanus-labs/vanus): Vanus is a Serverless, event streaming system with processing capabilities. It easily connects SaaS, Cloud Services, and Databases to help users build next-gen Event-driven Applications.
- [trpc-go](https://github.com/trpc-group/trpc-go): A pluggable, high-performance RPC framework written in Golang.
- [motan-go](https://github.com/weibocom/motan-go): a remote procedure call (RPC) framework for the rapid development of high-performance distributed services.
