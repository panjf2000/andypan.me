---
title: 【转】使用Address sanitizer自动检测Go应用中的内存错误
date: 2021-12-24
params:
  author: Fannie Zhang
summary: Go Address Sanitizer 初步介绍
tags:
 - Golang
 - Go tool chain
 - Go 工具链
isCJKLanguage: true
---

编者荐语：

Go 语言工具链活跃贡献者带你了解 Go 1.8 中新引入的 Address Sanitizer。

以下文章来源于[Golang Contributor Club](https://golangcn.org)，作者[Fannie Zhang](https://github.com/zhangfannie)

---

## 关于作者

本文作者[Fannie Zhang](https://github.com/zhangfannie)是Go社区的贡献者之一，主要致力于Go工具链的优化和特性的支持。本文介绍的Go中Address Sanitizer的特性是由本文作者实现的。

**「欢迎大家留言提供对Go Address Sanitizer的建议。如果它有帮助到发现实际应用中的错误，也希望可以留言分享。这样我们会更有动力来改进Go工具链。」**

## 前言

内存访问错误，包括缓冲区溢出和释放堆内存的使用，仍然是C和C++等编程语言的严重问题。这些错误既影响了系统的稳定性，也影响了程序的安全性，因为很多恶意代码可以通过内存错误来完成入侵。另外，内存错误的排查是困难的，很多时候导致问题的地方和发生问题的地方相隔甚远。Google旗下的开源工具Address Sanitizer可以帮助我们检测此类错误。

## Address Sanitizer

Address Sanitizer(ASan)是一个快速的内存错误检测工具，它可以检测以下问题：

  * 访问已被释放的内存
  * 堆上缓冲区访问溢出
  * 栈上缓冲区访问溢出
  * 全局缓冲区访问溢出
  * 内存泄漏

gcc 4.8版和LLVM 3.1版及以上支持了Address Sanitizer。

详细了解Address Sanitizer信息可以访问：[google/sanitizers](https://github.com/google/sanitizers/wiki/AddressSanitizer)。

## Go中集成Address Sanitizer

在Go中，开发者可以使用unsafe包对指针操作，引发内存访问错误。另外，Go语言可以通过CGO和C语言进行交互，内存可以在Go和C之间来回传递，这儿也会导致内存访问问题。在Go中集成Address Sanitizer能帮助检测出这些内存访问错误。

Go 1.18版支持了Address Asanitizer, 目前的实现只支持对go堆上缓冲区访问溢出的检查。

Go 1.18的版本说明：[Go 1.18 Release Notes](https://tip.golang.org/doc/go1.18)。

Go 1.18的二进制和源代码下载：[go1.18beta1](https://go.dev/dl/#go1.18beta1)。

## 在Go中使用Address Sanitizer

`go build`，`go run` 和 `go test` 等相关命令都支持 `-asan` 选项。当使用 `-asan` 选项时，工具链会自动在Go和C/C++代码中插入address sanitizer的支持。

下面以几个简单的测试程序为例，介绍 `-asan` 选项的用法和功能。

### case1.go

在这个程序中，go访问已被释放的非法C指针 `p` 。

```go
package main
/*
#include <stdlib.h>
#include <stdio.h>
int *p;

int* test() {
  p = (int *)malloc(2 * sizeof(int));
  free(p);
  return p;
}
*/
import "C"
import "fmt"

func main() {
  a := C.test()
  *a = 2         // BOOM
  fmt.Println(*a)
}
```

在终端运行命令 `go build -asan case1.go` 编译程序， 然后运行 `./case1` 程序会打印出下面的错误信息：

![](https://res.strikefreedom.top/static_res/blog/figures/c80eb340-aea5-46c9-8748-c83e94c90f61.png)

第一部分(ERROR): 指出错误类型use-after-free。

第二部分（WRITE)：指出线程名 T0， 操作为WRITE，发生的错误的位置。

第三部分（SUMMARY）：以上信息的一个总结。

### case2.go

在这个程序中，C访问的位置超出go数组 `cIntSlice` 的边界。

```go
package main

/*
#include <stdlib.h>
#include <stdio.h>
int *p;

void test(int *a) {
  int c = a[5];        // BOOM
  printf("a[5]=%d\n", c);
}
*/
import "C"

func main() {
  cIntSlice := []C.int{200, 201, 203, 203, 204}
  C.test(&cIntSlice[0])
}
```

同样，在终端运行命令 `go build -asan case2.go` 编译程序， 然后运行 `./case2` 程序，错误的信息被打印出：

![](https://res.strikefreedom.top/static_res/blog/figures/ce95ba89-f6a2-487e-b8c6-ee99361edeae.png)

### case3.go

这个程序通过使用 `unsafe` 包修改堆上分配的数组指针，引起越界访问的错误。

```go
package main

import (
        "fmt"
        "unsafe"
)

func main() {
        p := new([1024*1000]int)
        p[0] = 10
        r := bar(&p[1024*1000-1])
        fmt.Printf("r value is %d", r)
}

func bar(a *int) int {
        p := (*int)(unsafe.Add(unsafe.Pointer(a), 2*unsafe.Sizeof(int(1))))
        *p = 10 // BO0M
        return *p
}
```

使用 `-asan` 选项会检查出这个内存访问错误，错误的信息如下：

![](https://res.strikefreedom.top/static_res/blog/figures/8f9ae2f8-0c53-4219-a753-a95664c3ef5d.png)

##  更多

我们也实现了对全局缓冲区和栈上缓冲区访问错误的检查，但还未合入主分支。

## 原文

[使用Address sanitizer自动检测Go应用中的内存错误 —— Golang Contributor Club](https://mp.weixin.qq.com/s/dsfnuwkrcKpjmXJsKZzfYQ)