---
title: (13) go依赖管理
date: 2020-05-10 23:20:33
tags: [go]
---

本章主要介绍go语言依赖管理

<!--more-->

### go语言依赖管理三个阶段

> GOPATH -> GOVENDOR -> GOMOD

#### GOPATH（废弃）

第三方库依赖放在GOPATH下

${GOPATH}/src/

|- - our project

|- - lib1(src)

|- - lib2(src)

#### GOVENDOR（废弃）

> 每个项目有自己的vendor目录，存放第三方库

解决GOPATH下依赖不同版本的问题

${GOPATH}/src/

|- - our project

   |- - - vendor

​        |- - - - lib-common-v1

|- - lib1(src)

   |- - - vendor 

​        |- - - - lib-common-v2

|- - lib2(src)

#### GOMOD（推荐）

优势：

1. 支持镜像
2. 支持多版本管理
3. go.mod统一管理，简洁方便
4. 由go 命令统一管理，用户不必关心目录

> 初始化 go mod init <module>
>
> 添加依赖 go get
>
> 清理依赖 go mod tidy

#### 迁移至GOMOD

1. go mod init <moduleName>
2. go build ./...

#### GOPROXY国内镜像配置

```shell
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn,direct
```

