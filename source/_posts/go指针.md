---
title: (5)go指针
date: 2020-05-09 00:45:14
tags: [go]
---

本章主要介绍go语言指针用法及注意事项

<!--more-->

### go语言指针不能参与运算，但可以改变指向

```go
func main() {
	var a int = 2
	var pa *int = &a
	*pa = 3
	fmt.Println(a, pa, *pa, &pa, &a) //3 0xc00000a0e8 3 0xc000006028 0xc00000a0e8
}
```

### go语言函数参数是值传递

go语言只有值传递一种形式：

默认通过**数据拷贝**进行值传递，可通过传递指针实现传递引用的效果

因此在设计对象时就应该考虑传递形式

```go
func passByValue(a int) {
	a++
}
func passByRef(a *int) {
	*a++
}
func main() {
	a := 2
	passByValue(a)
	fmt.Println(a) //2
	passByRef(&a)
	fmt.Println(a) //3
}
```

### go语言支持多重赋值

两个变量交换不需要引入第三个参数

```GO
a,b int=3,4
a,b=b,a
```

### 交换函数实现

```go
//推荐做法
func swapNoSideEffect(a, b int) (int, int) {
	return b, a
}
func swap(a, b *int) {
	*a, *b = *b, *a
}
func main() {
	c, d := 5, 6
	swap(&c, &d)
	fmt.Println(c, d) //6,5

	c, d = swapNoSideEffect(c, d)
	fmt.Println(c, d) //5,6
	var e int
	c, d, e = d, c, d/c
	fmt.Println(c, d, e) //6 5 1 将右侧变量先运算替换，然后一次赋多值
}
```

