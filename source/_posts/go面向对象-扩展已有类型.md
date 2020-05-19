---
title: (12) go面向对象-扩展已有类型
date: 2020-05-10 23:19:34
tags: [go]
---

本章主要介绍go语言中如何扩展已有类型

<!--more-->

### 包的规定

1. 为结构定义的方法必须放在同一个包内
2. 可以是不同文件

> 基于包的规定，如何扩展系统类型和已有类型？

### 如何扩充

#### 使用组合

扩展tree.Node,新的包装对象新增后序遍历

```go
//扩展已有类型
type myTreeNode struct {
	node *tree.Node
}

func (myNode *myTreeNode) postOrder() {
	if myNode == nil || myNode.node == nil {
		return
	}
	left := myTreeNode{myNode.node.Left}
	left.postOrder()
	right := myTreeNode{myNode.node.Right}
	right.postOrder()
	myNode.node.Print()
}

func main() {
	var root tree.Node

	fmt.Println(root) //{0 <nil> <nil>}

	root = tree.Node{Value: 3}
	root.Left = &tree.Node{}
	root.Right = &tree.Node{Value: 5, Left: nil, Right: nil}
	//指针也可用.
	root.Right.Left = new(tree.Node)
	root.Left.Right = tree.CreateNode(2)

	root.Right.Left.SetValue(4)

	root.Traverse() //0 2 3 4 5

	fmt.Println()

	myNode := myTreeNode{&root}
	myNode.postOrder() //2 0 4 5 3

}
```

#### 定义别名

```go
type Queue []int

//指针接收者传递的为地址值，操作时需要*(取指针)操作
func (q *Queue) Push(v int) {
	*q = append(*q, v)
}

func (q *Queue) Pop() int {
	head := (*q)[0]
	*q = (*q)[1:]
	return head
}

func (q *Queue) IsEmpty() bool {
	return len(*q) == 0
}
```

#### 使用内嵌

> 使用匿名的成员变量进行组合,语法糖,减少代码量,相当于对匿名成员的代理，不同于继承
>
> 新对象中定义相同方法时会shadow组合成员的同名方法，可通过组合成员的默认名称调用组合成员的原方法

```go
package embeded

import "helloworld/tree"

//内嵌 使用匿名的成员变量进行组合,语法糖,减少代码量,相当于对匿名成员的代理
type ENode struct {
	//默认名称为简单结构名Node
	*tree.Node
}

func (myNode *ENode) PostOrder() {
	if myNode == nil || myNode.Node == nil {
		return
	}
	left := ENode{myNode.Left}
	left.postOrder()
	right := ENode{myNode.Right}
	right.postOrder()
	myNode.Print()
}
//shadow method for tree.Node
func (myNode *ENode) Traverse() {
	fmt.Println("this is a shadow method for tree.Node")
}
```

```go
func main() {
	var root embeded.ENode

	fmt.Println(root) //{0 <nil> <nil>}

	root = embeded.ENode{Node: &tree.Node{Value: 3}}
	root.Left = &tree.Node{}
	root.Right = &tree.Node{Value: 5, Left: nil, Right: nil}
	//指针也可用.
	root.Right.Left = new(tree.Node)
	root.Left.Right = tree.CreateNode(2)

	root.Right.Left.SetValue(4)

	root.Traverse() //this is a shadow method for tree.Node

	root.Node.Traverse() //0 2 3 4 5
    
	fmt.Println()

	root.PostOrder() //2 0 4 5 3

}
```

### 别名和组合的比较

定义别名：最简单（无法与组合无缝切换，要修改代码）

使用组合：最常用

使用内嵌：省下很多代码（不直观）