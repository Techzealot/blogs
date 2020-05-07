---
title: go语言常量
date: 2020-05-07 22:41:21
tags: [go]
---

## 1. 常量

```go
const (
	d = 1
	e = 2
)

func consts() {
	const filename = "abc.txt"
	//常量可以当做各种类型使用，相当于文本替换
	const a, b = 3, 4
	var c int
	//此处无需强转
	c = int(math.Sqrt(a*a + b*b))
	fmt.Println(c)
}
```

## 2. 枚举类型

```go
func enums() {
	/*const (
		cpp    = 0
		java   = 1
		python = 2
		golang = 3
	)*/
	const (
		cpp    = iota //0
		_             //1
		python        // 2
		golang        //3
	)
	//b kb mb gb tb pb
	const (
		b  = 1 << (10 * iota) //1 << (10*0)
		kb                    //1 << (10*1)
		mb                    //1 << (10*2)
		gb                    //1 << (10*3)
		tb                    //1 << (10*4)
		pb                    //1 << (10*5)
	)
	fmt.Println(b, kb, mb, gb, tb, pb) //1 1024 1048576 1073741824 1099511627776 1125899906842624

	fmt.Println(cpp, python, golang) //0 2 3
}
```

**iota 自增量种子 可以参与运算**