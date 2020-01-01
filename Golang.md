## 安装

- 官网下载
- 需要 GOPATH 环境变量（用户 or 全局），其值为 go 的 workspace

## 基础语法

```go
package main

import (
	"fmt"
	"math/rand"
)

/**
 *@author xieziwei99
 *2019-12-27
 */

func add(x, y int) int {
	return x + y
}

func swap(x, y string) (string, string) {
	return y, x
}

func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

var java bool

func main() {
	rand.Seed(86)
	// defer 语句会将函数推迟到外层函数返回之后执行。
	// 推迟调用的函数其参数会立即求值，但直到外层函数返回前该函数都不会被调用。
	defer fmt.Println("I am ", rand.Int(), add(4, 3))

	// 声明并赋值
	// := 结构不能在函数外使用
	a, b := "hello", "aloha"
	a, b = swap(a, b)			// 赋值
 	defer fmt.Println(a, b)		// defer 栈

	fmt.Println(split(19))

	var c = 0
	fmt.Println(c, java)

	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)

	for sum < 1000 {	// equals to while
		sum += sum
	}
	fmt.Println(sum)

	for {
		sum -= sum / 10
		if sum < 100 {
			break
		}
	}
	fmt.Println(sum)

	if temp := 100; sum < 100 {
		fmt.Println(temp)
	} else {
		fmt.Println(sum)
	}

	switch {
	case sum < 100:
		fmt.Println("hello")
	case sum < 1000:
		fmt.Println("Hi")
	}
}
```

## 进阶语法

```go
package main

import (
	"fmt"
	"math"
)

/**
 *@author xieziwei99
 *2019-12-27
 */

type Point struct {
	x int
	y int
}

func compute(fn func(a, b float64) float64) float64 {
	return fn(3, 4)
}

// 闭包
func adder() func(a int) int {
	sum := 0
	return func(a int) int {
		sum += a
		return sum
	}
}

// 闭包的另一个例子
func fibonacci() func() int {
	a, b := 0, 1
	return func() int {
		a, b = b, a+b
		return a
	}
}

func main() {
	i := 42
	p := &i
	fmt.Println(*p)
	*p = 20
	fmt.Println(i)

	point1 := Point{1, 2}
	point1.x = 20
	pPoint1 := &point1
	pPoint1.y = 19 // 这是 (*pPoint1).y 的缩写
	fmt.Println(point1)

	var a [2]string
	a[0] = "Hello"
	a[1] = "world"
	fmt.Println(a)

	primes := [6]int{2, 3, 5, 7, 11, 13}
	// s 是切片，就相当于 primes 的引用，更改切片，也会修改原数组
	s := primes[1:4] // include index 1, but exclude index 4
	fmt.Println(s)
	s[2] = 77
	fmt.Println(primes)
	// 切片的长度就是它所包含的元素个数
	// 切片的容量是从它的第一个元素开始数，到其底层数组元素末尾的个数
	fmt.Println(len(s), cap(s))

	num := 5
	// Invalid array bound 'num',must be a constant expression
	// var c [num]int
	aa := make([]int, num) // len(a)=5，用make时可以使用非 常值num，而不能用上面的方法
	b := make([]int, 0, 5) // len(b)=0, cap(b)=5
	b = b[:cap(b)]         // len(b)=5, cap(b)=5
	b = b[1:]              // len(b)=4, cap(b)=4
	fmt.Println(aa, b)
	aa = append(aa, 1, 2)
	fmt.Println(aa, len(aa), cap(aa))

	for i2, i3 := range aa {
		fmt.Println(i2, i3)
		i3 = 11 // i3只是副本，相当于foreach，不改变原数组（切片）
	}
	for i2 := range aa {
		fmt.Println(i2, aa[i2])
		aa[i2] = 11 // 可以更改
	}
	for _, v := range aa {
		fmt.Print(v, " ")
	}
	fmt.Println()

	aMap := make(map[int]string)
	aMap[5] = "mama"
	fmt.Println(aMap)
	aaa, in := aMap[1]
	if in {
		aaa += "a"
		fmt.Println(aaa)
	} else {
		aaa = "a"
		fmt.Println(aaa)
	}

	fmt.Println(compute(math.Max))
	fmt.Println(compute(math.Pow))

	add := adder()
	for i := 0; i < 10; i++ {
		fmt.Println(add(i))
	}

	f := fibonacci()
	for i := 0; i < 10; i++ {
	    fmt.Print(f(), " ")
	}
	fmt.Println()
}
```

