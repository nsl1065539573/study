####  1. 返回值

#####  1.1.1 函数返回值

函数返回值指的是函数经逻辑处理后返回给调用方的值。

Go的函数可以声明多个返回值，接受者如果不需要可以使用"_"来忽略对应位置的返回值

```go
package main

import "fmt"

func main() {
	_, x := test()
	fmt.Println(x)
}

func test() (int, int) {
	return 1, 2
}
```

Go 的返回值可以被命名，并且就像在函数体开头声明的变量那样使用。

```go
package main

import "fmt"

func main() {
	// _, x := test()
	// fmt.Println(x)
	sum, avg := test2(2, 4)
	fmt.Printf("sum is %v, avg is %v", sum, avg)
}

func test2(x, y int) (sum int, avg int) {
	sum = x + y
	avg = sum / 2
	return sum, avg
}
```

没有参数的 return 语句返回各个返回变量的当前值。这种用法被称作“裸”返回。

但是为了程序可读性，不建议如此使用

如第二个例子，我们直接return而并未对avg进行操作，其取的是int类型的默认值0

```go
// 没有参数的 return 语句返回各个返回变量的当前值。这种用法被称作“裸”返回。
// 但是为了程序可读性，不建议如此使用
func test3(x, y int) (sum int, avg int) {
	sum = x + y
	avg = sum / 2
	return
}

func test4(x, y int) (sum int, avg int) {
	sum = x + y
	// avg = sum / 2
	return
}
```

命名返回值会被本函数内生命的局部变量覆盖

```go

```



具有多个返回值的函数可以作为参数传递给下一个函数

```go
package main

import "fmt"

func main() {
	x := test4(test2(2, 4))
	fmt.Printf("可以被其他函数调用%v", x)
}

// Go 的返回值可以被命名，并且就像在函数体开头声明的变量那样使用。
func test2(x, y int) (sum int, avg int) {
	sum = x + y
	avg = sum / 2
	return sum, avg
}

// 多个返回值的函数可以直接被其他函数调用
func test4(x, y int) int {
	return x + y
}
```

