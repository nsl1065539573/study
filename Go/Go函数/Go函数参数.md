[TOC]



####  1. 函数参数

在函数声明时，可以声明函数的参数，称为形参。形参就像是定义在函数中的局部变量，当外部调用该函数时，传入的参数就是实参。

Go中提供两种传递参数的办法：

#####  值传递

> 指的是传递的是值得拷贝，在函数中对该参数进行修改不会影响到外部的变量

#####  引用传递

>  传递的是地址的拷贝，内部更改会更改外部的数据，因为更改的是该地址的值

在Go中，map、slice、chan、指针、interface默认以引用的方式传递。

##### 1.1.1 不定参数传值

> 不定参数就是参数的数量是不确定的，但是类型是确定的

```go
package main

import "fmt"

func main(){
	fmt.Println(test(1, 2, 3, 4, 5))
}

func test(args ...int) string {
	sum := 0
	for _, x := range args {
		sum = sum + x
	}
	return fmt.Sprintf("sum is %v", sum)
}
```

**需要注意的是**，含有不定参数的方法中，不定参数只能是最后一个参数，并且只能含有一个不定参数（因为如果不止一个的话，在前面的不定参数必然不是该方法的最后一个参数）：

<img src="D:\study\images\不定参数.png"/>

**可以使用切片对不定参数传值：**

使用切片对不定参数传值时，必须将切片展开：

```go
package main

import "fmt"

func main(){
	fmt.Println(test(1, 2, 3, 4, 5))
	arr := []int{1, 2, 3, 4, 5}
	fmt.Println(test(arr...))
}

func test(args ...int) string {
	sum := 0
	for _, x := range args {
		sum = sum + x
	}
	return fmt.Sprintf("sum is %v", sum)
}

```

#####  1.1.2 函数作为参数

函数也可以作为参数进行传递，上一讲中有说明

```go
package main

import "fmt"

func main(){
	s1 := test(func(x int) string {
		return string(x)
	}, 10)
	s2 := format(func(s string, x, y int) string {
        return fmt.Sprintf(s, x, y)
	}, "%d, %d", 10, 20)
	fmt.Println(s1)
	fmt.Println(s2)
}

func test(fn func(x int) string, i int) string {
	return fn(i)
}

type Format func(s string, x, y int) string

func format(f Format, s string, x, y int) string {
	return f(s, x, y)
}

func sum(x, y int) int {
	return x + y
}
```

