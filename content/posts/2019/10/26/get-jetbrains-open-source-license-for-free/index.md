---
title: 如何免费获取正版 JetBrains 全家桶
date: 2019-10-26
params:
  author: Andy Pan
summary: 教你如何不花钱就使用 JetBrains 全系列的产品！
tags:
 - 开源
isCJKLanguage: true
---

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/jetbrains/jetbrains.png)

## 前言

现在一提到 IDE，我相信很多人应该第一反应会是大名鼎鼎的 JetBrains 系列，这家位于捷克的软件开发公司出品一系列广受欢迎的 IDE，覆盖了目前最主流的几种编程语言/平台：C/C++ (CLion)、Java (IDEA)、Go (GoLand)、Python (PyCharm)、Ruby (RubyMine)、PHP (PhpStorm)、.NET (Rider)、JavaScript (WebStorm) 等等...

我个人很早以前用的已经是 JetBrains 全家桶来开发各种不同编程语言的应用了，首先是 JetBrains 系列的 IDE 的确做的很好，功能组件非常丰富，使用起来也很方便，有助于提高编码效率；其次是有完备的 Plugin Marketplace 提供丰富的插件支持，使用者能够安装各式各样的插件到 IDE 里，弥补一些特殊功能的缺失，比如 vim、git 等等，所以个人比较偏好 JetBrains 系列产品。

另外，一直以来关于 IDE vs editor + plugins 的争论，究竟是直接用 IDE 还是用 editor (Visual Studio Code、Sublime Text) 我觉得萝卜青菜各有所爱吧，没必要上升到开发者个人技术水平，我个人比较喜欢用 IDE，因为我不觉得你自己用一个 editor 加一堆开源插件配置出来的开发环境会比专业的软件开发团队经年累月打造出来的 IDE 更好用，专业团队考虑的肯定比你周全，可能有些开发者会说自定制更灵活，可以添加很多自己习惯的或者喜欢的 features，但是其实现在 JetBrains 已经有很丰富的插件支持了，基本上所有开发需要的插件都可以在它的插件市场找到并安装，而且也有越来越多的开发者在给 JetBrains 提供插件支持，所以我觉得 JetBrains IDEs 同样也可以满足开发者的自定制需求了。

我以前也喜欢干这种事：全部都是自己配置，用 Vim、Sublime Text、VSCode 加一堆插件自己搞一个 IDE，但是使用起来还是没有 JetBrains 全家桶方便，遂弃坑。总之，这个事情见仁见智吧，喜欢用 IDE 就用 IDE，喜欢自己配置就自己配置，没必要天天争这个。

## JetBrains 全家桶

JetBrains 系列产品不仅是出了名的好用，而且是出了名的贵。。。全家桶一年的授权使用费用如下：

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/jetbrains/jb-price.png)

这个价格，对于那些 JetBrains 的正版使用者：

![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1572082613694&di=f30838c36bb4b0c0c0cf8c85321be170&imgtype=0&src=http%3A%2F%2Fpic2.zhimg.com%2Fv2-8399dc9a6e579dd83df397b963a3122d_b.jpg)

### 免费获取 JetBrains 全家桶

#### 教育版授权证书

JetBrains 有专门的 educational licenses，供高校学生和教职工免费使用他们的系列产品，高校在校学生/在职教职工均可免费申请 JetBrains 教育版授权，享受 JetBrains 全家桶。

申请方法（直接翻译自英文官网）：

> JetBrains 提供了几种教育版免费授权证书的申请方式，最方便和快捷的方式是提供申请者的大学官方邮箱（edu 邮箱）或者是国际学生卡，一般来说通过这种方式只需要几分钟即可获得 JetBrains 全系列产品的免费教育授权证书。
>
> 申请者也可以通过提供学生/教师卡或者其他任何能够证明申请者是在校学生/在职教师身份的官方文件，这种申请方式可能需要好几天才能走完流程。

详情看这里：[JetBrains For Students](https://www.jetbrains.com/student/)，教育版证书的授权范围覆盖 JetBrains 全系列产品。

#### 开源软件授权证书

教育版证书只能是高校学生/教师方可申请，对于我们这种上了年纪的社畜来说就不用想了（哭，那么社畜就没有盼头了吗？幸好，天无绝人之路，以前我一直是用的 JetBrains 系列产品的社区版，其实社区版的功能已经基本能满足大多数的开发需求了，但是有些 IDE 没有社区版，只有收费版，这就比较蛋疼了，前阵子收到了官方发来的邮件，提醒说官方有一种 **Open Source License**，通过提交开发者的开源项目向官方申请这种开源证书免费授权，即可享受 JetBrains 全家桶，我只能说 JB 公司真是业界良心了。。。一个大大的赞！👍

##### 准备开源项目

从申请页面来看，官方并没有对申请者提交的开源项目有 star、fork 或 commit 数做任何限制，仅仅是要求该项目必须至少有 3 个月的活跃开发状态，还不需要是最近的 3 个月，简直良心得过分了好吗！！！不过我猜 star 数什么可能还是会有点加分，不过既然官方都不要求这些数据了，申请者也就不需要纠结了，大胆申请就行！

##### 提交申请

前置工作了解完成之后，就可以点进官方提供的 [Open Source License Request](https://www.jetbrains.com/shop/eform/opensource) 去提交申请了，我当时的申请表填写如下：

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/others/jb-ants.jpg)

关于这个申请表的填写：

1. `Tell us about your project`：我提供的开源项目是一个 Go 协程池：[ants](https://github.com/panjf2000/ants)，里面的项目名、描述、开发语言、年龄和证书等等如实填写即可；
2. `No. of required licenses`：申请的 license 数量，这个的意思是你要申请多少个 license，根据下面的说明，JetBrains 只会给该开源项目活跃的贡献者颁发证书，但是具体评判一个贡献者是否活跃的标准却没有提及，`ants` 目前的代码贡献者加上我一共有 9 位，事实上可能除了我以外其他人都算不上活跃的贡献者，因为大多情况是 `ants` 的使用者提了 bug-fix 或者 new-feature 的 PR，但是我还是尝试着在这个框填了一个 2。
3. `Tell us about yourself`：如实填写即可，一般来说需要提供 Github/GitLab 等的用户个人主页和注册邮箱供官方审校。

申请信息填写完成之后，勾选同意页面最下方的三个协议，最后点击 `APPLY FOR FREE LICENSES` 按钮提交申请，整个流程就完成了。

##### 申请获批

我在上周周六（2019-10-19）提交的申请，于本周周四（2019-10-24）收到证书申请通过的邮件，中间除去周末，审核周期大概 4 个工作日左右（这个不是官方的审核周期，只是我个人情况，具体以各个申请者的实际情况为准），最终 JetBrains 官方给我颁发了我要求的 2 个许可证，感谢金主爸爸！！！

邮件 subject 以 `License Certificate for JetBrains All Products Pack ` 开头：

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/others/jb-email.png)

几个关键信息：

> Your free subscription(s) will be valid for one year. You will receive an automatic email reminder shortly before the licenses expire. If your project still meets our [Support Program's requirements](https://www.jetbrains.com/buy/opensource/) at that time, feel free to apply for a renewal.

免费开源软件证书授权有效期是一年，证书到期前会有邮件通知。不过如果到期之后，开源项目依然符合该证书的申请条件，可以再一次申请新的证书，继续使用 JetBrains 产品。

> Please note that JetBrains OS licenses can be used only for development of your non-commercial open source project. Please do not use them for any commercial purposes. If you’ve received more than one license, please share them only with active contributors to your project.

该开源软件免费授权只能用于个人项目而非商业项目的开发，不能将其用于商业性用途。如果申请之后获得多个许可证，请仅和该项目的活跃开发者分享多余的许可证。

> We hope that JetBrains software will be useful for your project! If you feel that our tools have helped you a great deal, we would be grateful if you could mention JetBrains' support on your project's homepage. You could do this by placing [our Logo](https://www.jetbrains.com/company/press/#images-logos) and a link to JetBrains such as https://www.jetbrains.com/?from=ABC (make sure to replace ‘ABC’ with the name of your OS project, no spaces). Thanks in advance!

大概意思是说，如果许可证获得者觉得这个免费授权证书对你的开发工作有很大的帮助，希望你可以在你的项目主页里提及到 JetBrains 的支持，比如把他们公司的 logo 和 链接放置在项目主页上。

##### 激活许可证

点击邮件里的链接 `Take me to my license(s)` 登陆官网账号，登陆邮箱必须是之前提交申请之时填写的邮箱，如果该邮箱还没注册，则需要用它注册一个新的 JetBrains 账号，然后你会收到一封激活 licenses 的邮件，点击指定的链接进入激活页面：

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/others/jb-licenses.png)

通过浏览器访问激活链接，系统就会自动从证书池中取出一个证书激活，证书持有者可以随时撤回分享出去的许可证。

最后，授权证书生成的页面如下：

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/others/jb-license1.png)

点击页面上对应的 IDE 下载链接，下载安装你要使用的 IDE，打开 IDE，它会要求你激活，在 `Activate license with` 选项中选择 `JetBrains Account`，填上你之前申请许可证之后注册的 JetBrains 账号的邮箱和密码，点击 `Activate` 按钮，成功激活。

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/others/jb-activation.png)

以后再打开 IDE，启动页就会显示你的授权信息：

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/others/jb-goland.png)

##### 添加 JetBrains logo

我们完成了 JetBrains 开源软件证书免费许可证的申请和使用，节省了 649 美刀/1年的费用，为了表达对 JetBrains 支持开源的谢意，虽然 JetBrains 没有强制要求申请者把 JetBrains 的 logo 放置到项目主页上，但还是希望各位成功申请到免费许可证的同学能够在你们的个人项目主页里加上 JetBrains 的 logo，毕竟知恩图报嘛~~

我的项目已添加，参考如下：

![](https://raw.githubusercontent.com/panjf2000/illustrations/master/others/ants-jb-logo.png)

## 末

至此，本文就已经把就如何合法、正当地免费使用 JetBrains 全家桶的方法写完了，希望对各位同学有所帮助，如果觉得本文对你有那么一丝帮助的话，不妨点一下右下角的 `好看`，分享给有需要的朋友阅读，独乐乐不如众乐乐嘛！

最后，本公众号长期分享编程技术、文学历史、动画动漫类的文章，有兴趣的同学可以关注一下，相信我，你们不会后悔的~~
