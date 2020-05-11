---
title: (8) go map
date: 2020-05-10 23:17:16
tags: [go]
---

本章主要介绍go语言map的定义及使用

<!--more-->

### map的声明和初始化

> m :=map[K]V
>
> 1. map声明的时候默认值是**nil** ，此时进行取值，返回的是**对应类型的零值**
> 2. 两种初始化方式：map[K]V{}或make(map[K]V,cap)
> 3. 通过fmt打印map时，空map和nil map结果都为map[],所以断定map是空还是nil应该通过map == nil来判断
> 4. 未初始化的map是nil，它与一个空map基本等价，只是nil的map不允许往里面添加值。（A nil map is equivalent to an empty map except that no elements may be added）因此，map是nil时，取值是不会报错的（取不到而已），但增加值会报错。其实，还有一个区别，delete一个nil map会panic，但是delete 空map是一个空操作（并不会panic）（这个区别在最新的Go tips中已经没有了，即：delete一个nil map也不会panic）

```go
func main() {
	//1
	m := map[string]string{
		"name": "tom",
		"age":  "20",
	}
	fmt.Println(m) //map[age:20 name:tom]

	//2 或m2 := make(map[string]int,cap)
	m2 := make(map[string]int)
	fmt.Println(m2) //map[]

	m2["a"] = 1
	fmt.Println(m2)
	//3
	var m3 map[string]int
	fmt.Println(m3) //map[]

	//** this will panic
	m3["a"] = 1
    //this will not panic now
    delete(m3, "test")
}
```

### map的遍历

> map底层为hashmap，是无序的
>
> 使用len(m)可获取元素个数

```go
for k, v := range m {
	fmt.Println(k, v)
}
//只获取key
for k := range m {
	fmt.Println(k)
}
//只获取v
for _,v := range m {
	fmt.Println(v)
}
```

### map的get

> 当获取不存在的key时会返回zero value
>
> v,exists=map[key]

```go
if v, ok := m["test"]; ok {
    fmt.Println(v, ok)
}
```

### map的删除

```go
delete(m,key)
```

### map的k,v的类型

#### k的类型

1. map使用hash表，因此key必须可以比较相等
2. 除slice,map,function外均可作为key
3. 不包含slice,map,function字段的struct也可作为key

#### 类型的比较

1. slice不可以 s1 ==s2,只可以与nil比较，但可以借助于reflect.DeepEqual(arr1, arr2)比较，返回true或false,此外map也可以通过reflect.DeepEqual(m1, m2)比较

2. 不同结构的结构体不可以比较，但同一类型的实例值是可以比较的。

   **两个 struct完全相等， 意味着里面的所有变量的值都完全相等**

#### v的类型

1. 可以为任意类型

### map作为函数的参数

> Golang中是没有引用传递的，均为值传递。这意味着传递的是数据的拷贝。
> 那么map本身是**引用类型**，作为形参或返回参数的时候，传递的是**地址的拷贝**，**扩容**时也**不会改变**这个地址。

```go
func deleteMap(m map[string]string) {
	delete(m, "name")
}

func main() {
	//1
	m := map[string]string{
		"name": "tom",
		"age":  "20",
	}
	fmt.Println(m) //map[age:20 name:tom]
	
	deleteMap(m)
	fmt.Println("after delete:", m) //after delete: map[age:20]
}
```

### map的底层数据结构

todo

### map的并发安全

1. map是非并发安全的
2. 可以使用sync.Map

### map的gc回收机制

1. map在golang里是只增不减的一种数组结构，在删除的时候会标记说明该内存空间已经empty，不会回收

2. delete是不会真正的把map释放的，所以要回收map还是需要设为nil

### map实战案例：寻找最长不含重复字符的子串