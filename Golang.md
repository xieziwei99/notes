[TOC]

## 安装

- 官网下载
- 需要 GOPATH 环境变量（用户 or 全局），其值为 go 的 workspace

## 基础语法

### 除号

- 与 C 语言的除号相同

### 交换两个数

- ```go
  left, right = right, left
  fmt.Println(left, right)
  ```

### Switch

- Go里面`switch`默认相当于每个`case`最后带有`break`，匹配成功后不会自动向下执行其他case，而是跳出整个`switch`, 但是可以使用`fallthrough`强制执行后面的case代码。

### []byte & string 互转

```go
[]byte("Hello web go")

string(page2.Body)	// page2.Body为[]byte
```

### int 与 string 互转

``` go
intV, _ := strconv.Atoi("12")
fmt.Println(intV)
fmt.Println(strconv.Itoa(intV))
```

### 总览

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

### 总览

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



## 方法

### 为结构体定义方法

- 接收者的类型定义和方法声明必须在同一包内；
- 不能为内建类型声明方法。

```go
package main
// import 。。。

type Point struct {
	x, y float64
}

// 相当于toString()，实现Stringer接口
func (p Point) String() string {
	return fmt.Sprintf("Point(%v, %v)", p.x, p.y)
}

// 表示为结构体Point的方法
func (p Point) abs() float64 {
	return math.Sqrt(p.x*p.x + p.y*p.y)
}

// 指针接收者，可以改变调用者p的字段值
func (p *Point) plus(a float64) {
	p.x += a
	p.y += a
}

func main() {
	p := Point{3, 4}
	p.plus(10)	// 等价于(&p).plus(10)
	fmt.Println(p.abs())
}
```

### 接口

```go
package main
import "fmt"

type Speaker interface {
	say()
}

type Cat struct {
	name string
	age int
}

// 表示Cat类型实现了接口Speaker，但我们无需显式声明此事。
func (c Cat) say() {
	fmt.Printf("My name is %v, I came from the Cat family and I'm %v years old\n", c.name, c.age)
}

type Dog struct {
	name string
	age int
}

func (d *Dog)say() {
	fmt.Printf("My name is %v, I came from the Dog family, and I'm %v years old\n", d.name, d.age)
}

// 空接口，可接受未知类型的参数
func sayHello(s interface{}) {
	fmt.Printf("(%v, %T)\n", s, s)
}

func main() {
	cat := Cat{"Kitty", 18}
	dog := Dog{"doge", 19}

	var speaker Speaker
	speaker = cat
	speaker.say()

	speaker = &dog
	speaker.say()

	sayHello(12)
	sayHello(12.12)
	sayHello("12")
}
```

### 类型断言和类型选择

```go
// 类型选择
func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Println("I am a Integer", v)
	case string:
		fmt.Println("I am a String", v)
	default:
		fmt.Printf("I don't konw what this type %T means\n", v)
	}
}

func main() {
	var i interface{} = "hello"
	s := i.(string)		// 类型断言
	fmt.Println(s)

	s2, ok := i.(string)
	fmt.Println(s2, ok)

	integer2, ok := i.(int)		// not panic
	fmt.Println(integer2, ok)

	//integer := i.(int)	// panic
	//fmt.Println(integer)

	do(12)
	do("12")
	do(12.12)
}
```

### error 接口

```go
package main

import (
	"fmt"
	"strconv"
)

// 自定义错误类型
type MyError struct {
	code int
	desc string
}

func (e MyError) Error() string {
	return fmt.Sprintf("{\n\t错误码: %v,\n\t描述: %v\n}", e.code, e.desc)
}

func throwError() error {
	return MyError{code: 404, desc: "not found",}
}

func main() {
	num, err := strconv.Atoi("1i2")
	if err != nil {
		fmt.Println("转换失败", err)
	} else {
		fmt.Printf("(%v: %T)", num, num)
	}

	err = throwError()
	if err != nil {
		fmt.Println(err)
	}
}
```

```go
package main

import (
	"fmt"
)

func Sqrt(x float64) (float64, error) {
	if x >= 0 {
		return x, nil
	} else {
        // 将x转为ErrNegativeSqrt类型，它是error类型，输出时会调用x.Error()
		return 0, ErrNegativeSqrt(x)
	}
}

type ErrNegativeSqrt float64

// 实现Error()接口，即相当于ErrNegativeSqrt是error类型，
// e作为值接受者，即e.Error()
func (e ErrNegativeSqrt) Error() string {
	return fmt.Sprintf("cannot Sqrt negative number: %v", float64(e))	// 需要转换为float64
    												// 不然会递归调用e.Error()，最后内存溢出
}

func main() {
	fmt.Println(Sqrt(2))
	fmt.Println(Sqrt(-2))
}
```

### Reader 接口

#### strings.reader

```go
package main

import ("fmt" "io" "strings")

func main() {
	reader := strings.NewReader("Hello reader!!!")
	buf := make([]byte, 8)
	for {
		n, err := reader.Read(buf)
		fmt.Printf("n=%v, err=%v, buf=%c, buf[:n]=%q\n", n, err, buf, buf[:n])
		if err == io.EOF {
			break
		}
	}
}
```

## 并发

### goroutine & channel

```go
package main

import "fmt"

func sum(s []int, channel chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
	channel <- sum
}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}
	channel := make(chan int)
	go sum(s[:len(s)/2], channel)
	go sum(s[len(s)/2:], channel)
	x := <-channel
	y := <-channel
	fmt.Println(y, x, y+x)
}
```

### 等价二叉查找树

```go
package learningdemo

import "math/rand"

/**
 *@author xieziwei99
 *2020-01-19
 */

type Tree struct {
	left *Tree
	value int
	right * Tree
}

func New(k int) *Tree {
	var t *Tree
	for _, v := range rand.Perm(10) {
		t = insert(t, (1+v)*k)
	}
	return t
}

func insert(t *Tree, v int) *Tree {
	if t == nil {
		return &Tree{nil, v, nil}
	}
	if v < t.value {
		t.left = insert(t.left, v)
	} else {
		t.right = insert(t.right, v)
	}
	return t
}

// 大写才可以被其他的包访问
func Walk(t *Tree, ch chan int)  {
	sendValue(t, ch)
	close(ch)
}

func sendValue(t *Tree, ch chan int) {
	if t != nil {
		sendValue(t.left, ch)
		ch <- t.value
		sendValue(t.right, ch)
	}
}

func Same(t1, t2 *Tree) bool {
	ch1, ch2 := make(chan int), make(chan int)
	go Walk(t1, ch1)
	go Walk(t2, ch2)
	for v1 := range ch1 {
		if v1 != <-ch2 {
			return false
		}
	}
	return true
}
```

### sync.Mutex

```go
package main

// import ...

type SafeCount struct {
	v     int
	mutex sync.Mutex
}

func (sc *SafeCount) addOne() {
	sc.mutex.Lock()
	sc.v++
	sc.mutex.Unlock()
}

func (sc *SafeCount) getV() int {
	sc.mutex.Lock()
	defer sc.mutex.Unlock()	// 函数退出后解锁
	return sc.v
}

func main() {
	safeCount := SafeCount{v: 0,}
	for i := 0; i < 1000; i++ {
	    go safeCount.addOne()
	}
	time.Sleep(time.Second)
	fmt.Println(safeCount.getV())
}
```

## Web

### 简单的 HelloWorld

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func sayHello(w http.ResponseWriter, r *http.Request)  {
	_ = r.ParseForm()
	fmt.Println(r.Form)
	fmt.Println(r.URL.Path)
	fmt.Println(r.Form["param1"])
	for k, v := range r.Form {
		fmt.Printf("(%v=%v)\n", k, v)
	}
	_, _ = fmt.Fprintln(w, "Hello Go!!!")
}

// 运行此单个文件即可，相当简单
func main() {
	http.HandleFunc("/", sayHello)
	err := http.ListenAndServe(":9090", nil)
	if err != nil {
		log.Fatal("ListenAndServe:", err)
	}
}
```

### 相关方法

#### r *http.Request

##### r.FormValue

```go
// 返回第一个名叫body的组件的内容，类型为字符串
body := r.FormValue("body")
```




