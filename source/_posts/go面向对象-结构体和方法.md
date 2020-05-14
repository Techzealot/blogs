---

title: (10) go面向对象-结构体和方法
date: 2020-05-10 23:18:20
tags: [go]
---

本章主要介绍go语言结构体的使用及方法的定义

<!--more-->

### 面向对象

> 1. go仅支持封装，不支持继承和多态，通过接口和组合实现面向对象
>
> 2. go只有struct，没有class，没有构造函数，但有各种灵活的构造方式

### 结构的创建

```go
type treeNode struct {
	value       int
	left, right *treeNode
}
//返回了局部变量，变量分配由编译器决定分配在堆上还是栈上
func createNode(value int) *treeNode {
	return &treeNode{value: value}
}

func main() {
	var root treeNode

	fmt.Println(root) //{0 <nil> <nil>}

	root = treeNode{value: 3}
	root.left = &treeNode{}
	root.right = &treeNode{5, nil, nil}
	//指针也可用.
	root.right.left = new(treeNode)

	nodes := []treeNode{
		{value: 3},
		{},
		{6, nil, nil},
	}
	fmt.Println(nodes)//[{3 <nil> <nil>} {0 <nil> <nil>} {6 <nil> <nil>}]
}
```

> 不论地址还是结构本身都使用 . 来访问成员

### 定义方法

> 1. struct的方法就是普通函数，多声明了接收者
>2. 指针接收者和值接收者均可用实例去调用，由编译器根据真实情况决定传递值还是指针
> 3. 只有使用指针才能改变结构内部
>4. nil指针也可以调用方法

```go
func (t T) m(){
    
}
类似于
func m(t T){
    
}
因此struct 为nil时仍可以调用方法
```



```go
type treeNode struct {
	value       int
	left, right *treeNode
}

//值接收者，值传递
func (node treeNode) print() {
	fmt.Print(node.value," ")
}
//指针接收者
func (node *treeNode) setValue(value int) {
	node.value = value
}
```

> nil指针不同于null，nil可以调用方法，其他语言中null只能判断是否为null
>

```go
func (node *treeNode) nilTest() {
	if node == nil {
		fmt.Println("nil invoker")
	}
	//此处获取不到值，仍然会panic，但是可以调用进来
	//fmt.Println(node.value)
}
func main() {
	var pRoot *treeNode
	pRoot.nilTest() //nil invoker
}
```

### 中序遍历

```go
//中序遍历,递归
func (node *treeNode) traverse() {
	if node == nil {
		return
	}
	node.left.traverse()
	node.print()
	node.right.traverse()

}
func main() {
	var root treeNode

	fmt.Println(root) //{0 <nil> <nil>}

	root = treeNode{value: 3}
	root.left = &treeNode{}
	root.right = &treeNode{5, nil, nil}
	//指针也可用.
	root.right.left = new(treeNode)
	root.left.right = createNode(2)

	root.right.left.setValue(4)

	root.traverse() //0 2 3 4 5

}
```

### 值接收者vs指针接收者

1. 要改变内容必须使用指针接收者
2. 结构过大也考虑指针接收者
3. 一致性（建议，不强制）：如有指针接收者最好全为指针接收者
4. 值接收者为go特有
5. 值/指针接收者均可接收值/指针，亦即：**值接收者和指针接收者并不影响调用者的调用方式**