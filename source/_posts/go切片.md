---
title: (7) go切片
date: 2020-05-10 23:16:41
tags:  [go]
---

本章主要介绍slice的用法、扩展及操作

<!--more-->

### slice的定义

> slice是对数组的一个view 
>
> arr[m:n] 是对数组arr的一个slice,本身没有数据，是对数组arr从下标m到n-1的元素的view，为左闭右开，m和n省略是分别代表首尾

```go
func main() {

	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}

	fmt.Println("arr[2:6] =", arr[2:6]) //arr[2:6] = [2 3 4 5]

	fmt.Println("arr[2:] =", arr[2:]) //arr[2:] = [2 3 4 5 6 7]

	fmt.Println("arr[:6] =", arr[:6]) //arr[:6] = [0 1 2 3 4 5]

	fmt.Println("arr[:] =", arr[:]) //arr[:] = [0 1 2 3 4 5 6 7]

}
```

### slice作为参数

用作参数传递时表示形式为 ：[]T (数组定义不加长度即为slice)

```go
//将会更改slice对应的数组元素
func updateSlice(s []int) {
    //会更改slice的第0个元素对应的数组元素
	s[0] = 100
}

func main() {

	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}

	updateSlice(arr[2:])

	fmt.Println(arr) //[0 1 100 3 4 5 6 7]

	updateSlice(arr[:])

	fmt.Println(arr) //[100 1 100 3 4 5 6 7]

}
```

### reslice

slice可以再次slice,而且是对同一个数组的view

```go
func main() {

	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}

	s2 := arr[:][:5]
	fmt.Println("s2=", s2) //s2= [0 1 2 3 4]
	s3 := s2[2:]
	fmt.Println("s3=", s3) //s3= [2 3 4]

}
```

### slice的扩展

```go
func main() {

	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}

	s1 := arr[2:6]
	fmt.Println("s1=", s1) //s1= [2 3 4 5]

	//s1[4]不存在,但用slice时可以扩展
	s2 := s1[3:5]
	fmt.Println("s2=", s2) //s2= [5 6]

}
```

![slice的扩展示意图](/img/slice-extend.jpg)

### slice的底层实现

![slice的底层实现](/img/slice-base.jpg)

**slice可以向后扩展，但不可以向前扩展**

**s[i]不可以超越len(s),向后扩展不可以超越底层数组cap(s)**

```go
func main() {

	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}

	s1 := arr[2:6]
	fmt.Printf("s1=%v ,len(s1)=%d ,cap(s1)=%d \n", s1, len(s1), cap(s1)) //s1=[2 3 4 5] ,len(s1)=4 ,cap(s1)=6

	//s1[4]不存在,但用slice时可以扩展
	s2 := s1[3:5]
	fmt.Printf("s2=%v ,len(s2)=%d ,cap(s2)=%d \n", s2, len(s2), cap(s2)) //ss2=[5 6] ,len(s2)=2 ,cap(s2)=3

}
```

### slice的操作：添加，创建，复制，删除

> 1. 添加元素时如果超过底层数组cap，系统会重新分配更大的底层数组并会拷贝原来数组的数据
> 2. 由于append为值传递，且可能引起扩容(导致底层ptr,len,cap发生变化)，必须接收append的返回值
> 3. 固定用法：s =append(s,val)

#### 添加

```go
func main() {

	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}

	s1 := arr[2:6]
	s2 := s1[3:5]

	//arr[8]被覆盖为10
	s3 := append(s2, 10)

	s4 := append(s3, 10)

	s5 := append(s4, 10)

	//对切片的插入一旦超过底层数组cap，便会生成一个更大（2^n）的新数组并会拷贝原来数组的数据
	//s4 s5 no longer view arr
	fmt.Println("s3 ,s4 ,s5 =", s3, s4, s5) //s3 ,s4 ,s5 = [5 6 10] [5 6 10 10] [5 6 10 10 10]

	fmt.Println(arr) //[0 1 2 3 4 5 6 10]

}
```

#### 创建

> 1. var []T
> 2. s :=[]T{}
> 3. make([]T,len)
> 4. make([]T,len,cap)
> 5. arr[m:n]

```go
func printSlice(s []int) {
	fmt.Printf("%v ,len= %d ,cap= %d \n", s, len(s), cap(s))
}

func main() {

	var s []int //zero value for slice is []

	printSlice(s)

	s2 := make([]int, 16)

	s3 := make([]int, 10, 32)

	printSlice(s2)

	printSlice(s3)
}
```

#### 复制

> copy（dst,src） //把src的元素拷贝到dst中

```go
func printSlice(s []int) {
	fmt.Printf("%v ,len= %d ,cap= %d \n", s, len(s), cap(s))
}

func main() {

	var s []int //zero value for slice is []

	printSlice(s)

	s1 := []int{2, 4, 6, 8}

	s2 := make([]int, 16)

	s3 := make([]int, 10, 32)

	printSlice(s2)

	printSlice(s3)

	copy(s2, s1)

	fmt.Println(s2) //[2 4 6 8 0 0 0 0 0 0 0 0 0 0 0 0]
}
```

#### 删除

##### 删除第n个元素

> s=append(s[:n],s[n+1:]...)

```go
func main() {

	var s []int //zero value for slice is []

	printSlice(s)

	s1 := []int{2, 4, 6, 8}

	s2 := make([]int, 16)

	s3 := make([]int, 10, 32)

	printSlice(s2)

	printSlice(s3)

	copy(s2, s1)

	fmt.Println(s2) //[2 4 6 8 0 0 0 0 0 0 0 0 0 0 0 0]    
    //删除下标为3的元素
	s2 = append(s2[:3], s2[4:]...)

	printSlice(s2) //[2 4 6 0 0 0 0 0 0 0 0 0 0 0 0] ,len= 15 ,cap= 16
}
```

##### 删除首尾

```go
//删除头
s =s[1:]
//删除尾
s =s[:len(s)-1]
```

