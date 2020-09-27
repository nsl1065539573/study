[TOC]



####  1. 函数定义

#####  1.1.1 golang函数特点

```
 • 无需声明原型。
 • 支持不定 变参。
 • 支持多返回值。
 • 支持命名返回参数。 
 • 支持匿名函数和闭包。
 • 函数也是一种类型，一个函数可以赋值给变量。

 • 不支持 嵌套 (nested) 一个包不能有两个名字一样的函数。
 • 不支持 重载 (overload) 
 • 不支持 默认参数 (default parameter)。
```

#####  1.1.2 函数声明

- 函数声明包含一个func关键字，函数名，参数列表，返回值列表，方法体等，参数列表和返回值列表都可以空，或者都可以是多个
- 类型声明在变量之后
- 如果多个参数为同一类型，可以统一写到后面

```go
func sum(x, y int) int {
	return x + y
}
```

#####  1.1.3 函数可作为参数传递

函数也是一种数据结构，可以作为参数进行传递

```go
func test(fn func(x int) string, i int) string {
	return fn(i)
}

type Format func(s string, x, y int) string

func format(f Format, s string, x, y int) string {
	return f(s, x, y)
}
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
```

