---
title: (14)go接口
date: 2020-05-27 00:11:17
tags: [go]
---

本章介绍go语言接口的基本知识

<!--more-->

### 接口

一组行为的集合

```go
package inf

type Retriever interface {
	Get(url string) string
}
```

### 接口实现(duck typing)

接口实现是隐式的，无需显式声明，接口与实现分离，由使用者定义

具备接口定义的行为，就称实现了接口

```go
package infra

import (
	"io/ioutil"
	"net/http"
)

type Retriever struct {
}
//值接收者
func (Retriever) Get(url string) string {
	resp, err := http.Get(url)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	bytes, _ := ioutil.ReadAll(resp.Body)
	return string(bytes)
}
```

```go
package testing

type Retriever struct {
}

//指针接收者
func (*Retriever) Get(url string) string {
	return "this is a test website"
}
```

```go
package main

import (
	"fmt"
	"helloworld/downloader/inf"
	"helloworld/downloader/testing"
)

func main() {
	url := "https://www.imooc.com"
	retriever := getRetriever()
	fmt.Println(retriever.Get(url))

	typeSwitch(retriever)
	typeAssertion(retriever)
}

func getRetriever() inf.Retriever {
	return &infra.Retriever{}
	//return infra.Retriever{}
	//return &testing.Retriever{}
}
```

### 接口类型获取

#### switch

```go
func typeSwitch(retriever inf.Retriever) {
	switch retriever.(type) {
	case *testing.Retriever:
		fmt.Printf("%T ,%v\n", retriever, retriever)
	case infra.Retriever:
		fmt.Printf("value receiver: %T\n,%v\n", retriever, retriever)
	case *infra.Retriever:
		fmt.Printf("pointer receiver: %T\n,%v\n", retriever, retriever)
	default:
		fmt.Println("other retriever")
	}
}
```

#### type assertion

```go
func typeAssertion(retriever inf.Retriever) {
	if testRetriever, ok := retriever.(*testing.Retriever); ok {
		fmt.Printf("test : %T ,%v\n", testRetriever, testRetriever)
	}
	if infraRetriever, ok := retriever.(infra.Retriever); ok {
		fmt.Printf("infra : %T ,%v\n", infraRetriever, infraRetriever)
	}
}
```

### 接口变量的构成

#### 1.接口变量的类型

#### 2.接口变量的值或指针

具体是值还是指针取决于实现方法的接收者类型

#### 接口变量的使用

接口变量可以带有指针

接口变量采用值传递，由于可以包含一个指针因此几乎不需要使用接口变量的指针

指针接收者只能以指针方式使用，值接收者都可以

interface{}表示任何类型

```go
package genericqueue

type Queue []interface{}

//指针接收者传递的为地址值，操作时需要*(取指针)操作
func (q *Queue) Push(v interface{}) {
	*q = append(*q, v)
}

func (q *Queue) Pop() interface{} {
	head := (*q)[0]
	*q = (*q)[1:]
	return head
}

func (q *Queue) IsEmpty() bool {
	return len(*q) == 0
}
```

