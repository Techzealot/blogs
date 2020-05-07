---
title: go控制结构
date: 2020-05-07 23:30:44
tags: [go]
---

**本章主要介绍了go语言的流程控制结构if,switch,for的常见用法**

<!--more-->

## 1. if

form 1

```go
func main() {
	const filename = "abc.txt"
	file, err := ioutil.ReadFile(filename)
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("%s\n", file)
	}
}
```

form 2（**推荐**）

```go
func main() {
	const filename = "abc.txt"
    //file作用域在if语句内
	if file, err := ioutil.ReadFile(filename); err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("%s\n", file)
	}
}
```

## 2. switch

switch默认会break除非使用fallthrough

form 1

```go
func eval(a, b int, op string) int {
	switch op {
	case "+":
		return a + b
	case "-":
		return a - b
	case "*":
		return a * b
	case "/":
		return a / b
	default:
		panic("unsupported op:" + op)
	}
}
```

form 2:

switch后没有表达式，case中有表达式

```go
func grade(score int) string {
	g := ""
	switch {
	case score < 0 || score > 100:
		panic(fmt.Sprintf("wrong score %d \n", score))
	case score < 60:
		g = "F"
	case score < 80:
		g = "C"
	case score < 90:
		g = "B"
	case score < 00:
		g = "A"
	}
	return g
}
```

## 3. for 

form 1

for的条件里不需要括号

for可以省略初始条件、结束表达式、自增条件

```go
func loop() {
	sum := 0
	for i := 0; i < 100; i++ {
		sum += i
	}
}
```

form 2 省略初始条件

```go
func convertToBin(n int) string {
	if n == 0 {
		return "0"
	}
	result := ""
	for ; n > 0; n /= 2 {
		lsb := n % 2
		result = strconv.Itoa(lsb) + result
	}
	return result
}
```

form 3 省略结束条件、递增条件，此时相当于while

```go
func printFile(filename string) {
	file, err := os.Open(filename)
	if err != nil {
		panic(err)
	}
	scanner := bufio.NewScanner(file)
	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}
}
```

form 4 省略结束条件

```go
func forever() {
	for {
		fmt.Println("forever")
	}
}
```

