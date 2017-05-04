---
layout:     post
title:

subtitle:   Mac下配置LiteIDE 编译环境
date:       2017-05-04
author:     LM
header-img: img/post-bg-debug.png
catalog: true
tags:
    - GO
    - MAC
    - LiteIDE
---

# 前言
Mac下配置LiteIDE 编译环境



1.Mac下，我使用的go1.5.1.darwin-amd64.pkg 安装包直接下一步下一步即可
2.cd ~
```  
   mkdir bin 
   mkdir pkg
   mkdir src
```
3.LiteIDE,安装完毕，打开，点击查看->编辑当前环境，输入如下：

```
# native compiler drawin amd64
#/usr/local/go 为自动安装的go环境Mac下的目录
#GOPATH=$HOME/goworkspace 为我自己创建的工作目录，其中有src bin pkg 3个目录
GOROOT=/usr/local/go
GOPATH=$HOME/goworkspace
GOBIN=
GOARCH=amd64
GOOS=darwin
CGO_ENABLED=1
#GO15VENDOREXPERIMENT=1
PATH=$GOROOT/bin:$PATH
LITEIDE_GDB=/usr/local/bin/gdb
LITEIDE_MAKE=make
LITEIDE_TERM=/usr/bin/open
LITEIDE_TERMARGS=-a Terminal
LITEIDE_EXEC=/usr/X11R6/bin/xterm
LITEIDE_EXECOPT=-e
```
4.在src中创建main.go
```
package main
import "fmt"
func main() {
    fmt.Println("Hello world!")
}
```
5.点击上面的BR 按钮就可运行 build and run




