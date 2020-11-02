# 安装

## Tutorial: Get started with Go
[原文](https://golang.org/doc/tutorial/getting-started)


Golang V1.13之后支持通过设置变量GOPROXY来修改代理地址，默认的代理服务器
https://proxy.golang.org在国内访问经常出现timeout


可以通过设置国内代理来加速下载
详见：https://github.com/goproxy/goproxy.cn/blob/master/README.zh-CN.md
在终端执行即可

```sh
go env -w GOPROXY=https://goproxy.cn,direct
```

用`go env` 查看GOPROXY的值

```sh
➜  2 go version
go version go1.15.1 darwin/amd64
➜  2 go env -w GO111MODULE=on
➜  2 go env -w GOPROXY=https://goproxy.cn,direct
➜  2 echo "export GO111MODULE=on" > ~/.bashrc
➜  2 echo "export GOPROXY=https://goproxy.cn" > ~/.bashrc
➜  2 source ~/.bashrc
```

```sh
➜  tutorials go run test1.go
Welcome to the playground!
The time is 2020-09-07 16:52:54.404604 +0800 CST m=+0.000119450
➜  tutorials l
total 32
drwxr-xr-x  4 wuxiaoming  staff   128B  9  7 16:51 .
drwxr-xr-x  3 wuxiaoming  staff    96B  9  7 16:51 ..
-rw-r--r--  1 wuxiaoming  staff    12K  9  7 16:52 .test1.go.swp
-rw-r--r--  1 wuxiaoming  staff   142B  9  7 16:51 test1.go
```

# 遇到的问题

### if

```go
//报错
pos++
fmt.Println(pos)
if pos-1 == 0 {
    return 0
}
else if pos-1 == 1 {
    return 1
}
else {
	sum := first + sec
	first = sec
	sec = sum
	return sum
}

if pos-1==0{
    return 0
} else if pos-1==1{
    return 1
}
/* 格式一定得是
if condition{
} else if condition {
} else {
}
*/

```

# hello world

[原文](https://tour.golang.org/welcome/1)

## Basics

### Packages

Programs start running in package main.

### Imports

```go
package main
import (
	"fmt"
	"math"
)
import ( "fmt"; "math") // 必须有分号

import "fmt"
import "math"
```
两种都可以，前面好看一点

### Exported names

In Go, a name is exported if it begins with a capital letter. For example, `Pizza` is an exported name, as is `Pi`, which is exported from the `math` package.

`pizza` and `pi` do not start with a capital letter, so they are not exported.

大写的被导出了，小写不会

### Functions

```go
// 方法后面一定要有一个返回类型
func add(x int, y int) int {
	return x + y
}
func main() {
	fmt.Println(add(42, 13)
}
```

同样类型的参数，前面的类型可以省略

```go
func add(x , y int) int {
	return x + y
}
func main() {
	fmt.Println(add(42, 13)
}
```

### multiple result

A function can return any number of results.

```go
package main

import "fmt"

func swap(x, y string) (string, string) {
	return y, x
}

func main() {
    // 或者可以 var a, b = swap(...
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}
```

### named return values

A return statement without arguments returns the named return values. This is known as a "naked" return.

Naked return statements should be used only in short functions, as with the example shown here. They can harm readability in longer functions.

单独的一个return，会返回named return values，是按照返回类型里的参数名字返回，尽量只给小函数用，会影响长函数的可读性。

```go
func double_and_self(x int) (self, double int){

    self = x
    double =x * x
    return
}

func main(){

    fmt.Println(double_and_self(10))
}
```

### variables

var 可以定义一个变量，类型在后面，var 可以是package 或function 级别
: is used when you perform the short declaration and assignment for the first time as you are doing in your first statement
只有第一次赋值的时候，可以用`:=`

```go

var c, python, java bool
func main(){

    var a int
    fmt.Println(a, c, python, java)
}
//打印 0 false false false
```
### variables init

如果有初始化，类型可以省略

```go
var i, j int = 1, 2
func main() {
	var c, python, java = true, false, "no!"
	fmt.Println(i, j, c, python, java)
}
```

### short variable declaration

Inside a function, the := short assignment statement can be used in place of a var declaration with implicit type.

Outside a function, every statement begins with a keyword (var, func, and so on) and so the := construct is not available.

在方法里，可以用短赋值语句，在package 级别不能用，packeg 级别一定要关键字开头

```go
func main(){

    k = 2 // undefined k
    j = 3 // undefined j
    k := 2 // ok
    j := 3 // ok
    var k = 2 // ok
    var j = 3 // ok
    var k int = 2 // ok
    var j int = 3 // ok
    var k int = "fdsa" //cannot use "fdsa" (type untyped string) as type int in assignment
    var k int = 3 // ok
    var j string = "23213" // ok

    fmt.Println(k, j)
}
```

### basic types

基础数据类型

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128

```
The int, uint, and uintptr types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems. When you need an integer value you should use int unless you have a specific reason to use a sized or unsigned integer type.

想用整形，就用int，除非想指定长度，int 在32机器上是32位，64机器上是64

```go
var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main(){

    fmt.Println(MaxInt)  // 18......
    fmt.Println(z)  // (2+3i)
    fmt.Println(ToBe)  // false


    fmt.Printf("Type: %T Value: %v\n", ToBe, ToBe)
	fmt.Printf("Type: %T Value: %v\n", MaxInt, MaxInt)
	fmt.Printf("Type: %T Value: %v\n", z, z)

    //Type: bool Value: false
    //Type: uint64 Value: 18446744073709551615
    //Type: complex128 Value: (2+3i)
}
```

### zero value 默认值

Variables declared without an explicit initial value are given their zero value.

```
0 for numeric types,
false for the boolean type
"" (the empty string) for strings.

```

```go
func main(){
    var a int
    var b bool
    var c string
    var d float32
	var z complex128

	fmt.Printf("%v %v %q %v %v\n", a, b, c, d, z)
    //0 false "" 0 (0+0i)
}
```

### type conversions

The expression T(v) converts the value v to the type T.

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

Or, put more simply:

```go
i := 42
f := float64(i)
u := uint(f)
```

### constants

Constants are declared like variables, but with the const keyword.

Constants can be character, string, boolean, or numeric values.

Constants cannot be declared using the := syntax.

```go
const Pi = 3.14
```

### for 
Go has only one looping construct, the for loop.

分号分隔的3 部分，只是不带小括号，大括号一定要有

- the init statement: executed before the first iteration
- the condition expression: evaluated before every iteration
- the post statement: executed at the end of every iteration

```go
var sum = 0

for i:=0;i<=100;i++{
    sum += i
}

fmt.Println(sum)
```

init 和post 两个部分可省

```go

sum1 := 1
for ; sum1 < 1000 ; {
    sum1 += sum1
    fmt.Println(sum1)
}

```

for 转while

```go
sum2 := 2

for sum2<100{
    sum2 += 10
}
fmt.Println(sum2)
```
If you omit the loop condition it loops forever, so an infinite loop is compactly expressed.
无限循环

```go
for {
}
```

### if

if 和for 很像，不用小括号，要带上大括号
```go
if x < 0 {

}
```

### If with a short statement

Like for, the if statement can start with a short statement to execute before the condition.

Variables declared by the statement are only in scope until the end of the if.

(Try using v in the last return statement.)


```go
x := 10
if x < 100 {
    fmt.Println("x < 100")
}
if v := 2 + 2; v < 5 {
    fmt.Println(" x < 5")
}

//fmt.Println("v= %v" , v) //undefined: v
```

Variables declared inside an if short statement are also available inside any of the else blocks.

### switch

Go only runs the selected case, not all the cases that follow. In effect, the break statement that is needed at the end of each case in those languages is provided automatically in Go. Another important difference is that Go's switch cases need not be constants, and the values involved need not be integers.

1. 不需要break 也只跑那一个满足的case（类似于默认加上了break）
2. switch 语句不必是constants，也不必一定是int

### Switch with no condition

Switch without a condition is the same as switch true.

This construct can be a clean way to write long if-then-else chains.

无条件的switch，基本是用来写多个if else 用的

```go
t := time.Now()
switch {
case t.Hour() < 12:
	fmt.Println("Good morning!")
case t.Hour() < 17:
	fmt.Println("Good afternoon.")
default:
	fmt.Println("Good evening.")
}
```

### defer

A defer statement defers the execution of a function until the surrounding function returns.

The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.

1. 值被立即evaluate，但是执行是到最后执行
2. 在return 之前执行defer
3. 是个栈，先进后出

### Pointers

A pointer holds the memory address of a value.

The type *T is a pointer to a T value. Its zero value is nil.

```go
var p *int
```
The & operator generates a pointer to its operand.

```go
i := 42
p = &i
```
The * operator denotes the pointer's underlying value.

```go
fmt.Println(*p) // read i through the pointer p
*p = 21         // set i through the pointer p
```
This is known as "dereferencing" or "indirecting".

```go
i, j:=1, 2

p:= &i

fmt.Println(p, i, j) //0xc00001a090 1 2

*p = 11

fmt.Println(p, i, j) //0xc00001a090 11 2

p=2
//cannot use 2 (type int) as type *int in assignment
fmt.Println(p, i, j)
```

### Structs

A struct is a collection of fields.
Struct fields are accessed using a dot.

```go
type Vert struct {
    X int
    Y int
}

func main(){

    v := Vert{1,2 }

    fmt.Println(v) // {1 2}

    v.X=2
    fmt.Println(v) // {2 2}

    //v.Y = "fdfadfas" // cannot use "fdfadfas" (type untyped string) as type int in assignment

    p := &v
    fmt.Println(p, *p) // &{2 2} {2 2}
}
```

### Pointers to Structs

可以用p.X 访问结构体里的数据，不需要(*p).X

```go
func main() {
	v := Vertex{1, 2}
	p := &v
	p.X = 1e9 // 或者写成(*p).X
	fmt.Println(v) // {1000000000 2}
}
```

### Array

The type [n]T is an array of n values of type T.

The expression

```go
var a [10]int
```
declares a variable a as an array of ten integers.

An array's length is part of its type, so arrays cannot be resized. This seems limiting, but don't worry; Go provides a convenient way of working with arrays.

```go
func main(){

    fmt.Println()

    var a [2] string
    a[0] = "hello"
    a[1] = "world"

    fmt.Println(a[0], a[1]) // hello world
    fmt.Println(a) // [hello world]

    p := [6]int{1,2,3,4,5,6} //[1 2 3 4 5 6]
    fmt.Println(p)

    p = [7]int{1,2,3,4,5,6} // cannot use [7]int literal (type [7]int) as type [6]int in assignment
    // [7]int 和[6]int 不是同一个类型的
    fmt.Println(p)

    // slice
    var s []int = p[0:0]
    fmt.Println(s)   //[]

    s = p[0:1]
    fmt.Println(s)   //[1]


}
```
### slice

An array has a fixed size. A slice, on the other hand, is a dynamically-sized, flexible view into the elements of an array. In practice, slices are much more common than arrays.

The type []T is a slice with elements of type T.

A slice is formed by specifying two indices, a low and high bound, separated by a colon:

a[low : high]
This selects a half-open range which includes the first element, but excludes the last one.

The following expression creates a slice which includes elements 1 through 3 of a:

a[1:4]

A slice does not store any data, it just describes a section of an underlying array.

Changing the elements of a slice modifies the corresponding elements of its underlying array.

Other slices that share the same underlying array will see those changes.

1. 左闭右开区间
2. 其实是一个数组的一部分
3. 改了其中的值，数组也会改，其它的同一块的slice 的值也会改


```go
s1 := []int{1,2,3,4,5}   //[1 2 3 4 5]
s2 := []string{"1","2","3","4"} // [1 2 3 4]
s3 := []struct{
    age int
    name string
}{
    {1, "1"},
    {2, "2"},
    {3, "3"},
    {4, "4"}, // 这个逗号必须要有，不知道为什么
}

//[{1 1} {2 2} {3 3} {4 4}]
```

### slice default

默认不传start end 的话，就是全部。对var a [10]int 来说，`a[0:10] = a[:10] = a[0:] = a[:]`

```go
s4 := []int{1, 2, 3, 4, 5}
fmt.Println(s4[1:4]) // [2 3 4]
fmt.Println(s4[:2]) //[1 2]
fmt.Println(s4[1:]) //[2 3 4 5]
fmt.Println(s4[:]) // [1 2 3 4 5]
```

### slice length and capacity

A slice has both a length and a capacity.

The length of a slice is the number of elements it contains.

The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice.

The length and capacity of a slice s can be obtained using the expressions len(s) and cap(s).

You can extend a slice's length by re-slicing it, provided it has sufficient capacity

1. length 是slice的长度，capacity 是原数组的长度，用len() 和cap() 分别获取

### nil slice

The zero value of a slice is nil.

A nil slice has a length and capacity of 0 and has no underlying array.

空slice legnth capacity 都是0，无底下的数组，与nil 相等

```go
var s5 []int
fmt.Println(s5, len(s5), cap(s5))  // [] 0 0
fmt.Println(s5 == nil) // true
```

### make a slice

The make function allocates a zeroed array and returns a slice that refers to that array:

```go
a := make([]int, 5)  // len(a)=5
```

To specify a capacity, pass a third argument to make:

```go
b := make([]int, 0, 5) // len(b)=0, cap(b)=5

b = b[:cap(b)] // len(b)=5, cap(b)=5
b = b[1:]      // len(b)=4, cap(b)=4
```

```go
s6 := make([]int, 5)
fmt.Println(s6)  //[0 0 0 0 0]
fmt.Println(len(s6))  // 5

s7 := make([]int, 1, 5)
fmt.Println(s7) // [0]

s7 = make([]int, 0, 5)
fmt.Println(s7)// []
```

### append

```go
func append(s []T, vs ...T) []T
```

The first parameter s of append is a slice of type T, and the rest are T values to append to the slice.

If the backing array of s is too small to fit all the given values a bigger array will be allocated. The returned slice will point to the newly allocated array.

1. 如果后面的array 太小了，会重新创建一个新的array。第一次扩容是二倍的长度，再扩容就+2长度直到满，再+2长度 
2. 不会改变原来的slice

```go
s8 := []int{1,2,3,4,5}
printSlice(s8) // len=5 cap=5 [1 2 3 4 5]
s9 := append(s8, 6)
printSlice(s9) // len=6 cap=10 [1 2 3 4 5 6]

s8 = []int{1,2,3,4,5,6,7,8}
printSlice(s8)  // len=8 cap=8 [1 2 3 4 5 6 7 8]
s9 = append(s8, 6)
printSlice(s9) // len=9 cap=16 [1 2 3 4 5 6 7 8 6]

s9 = append(s8, 100, 101, 102, 103)
printSlice(s9) // len=12 cap=16 [1 2 3 4 5 6 7 8 100 101 102 103]，不会改变s8 的值

s9 = append(s8, 100, 101, 102, 103, 104, 105,106,107,108, 109, 110, 111, 112)
// 第一次一倍长度，第二次，原长度上加2，加2，直到长度再不够，再加2 长度
printSlice(s9) // len=21 cap=22 [1 2 3 4 5 6 7 8 100 101 102 103 104 105 106 107 108 109 110 111 112]


```

### range

The range form of the for loop iterates over a slice or map.

When ranging over a slice, two values are returned for each iteration. The first is the index, and the second is a copy of the element at that index.

```go
func main() {

	var s []int = []int{1, 2, 3, 4, 5, 6, 7, 8}
	// 也可以省略前面的类型，后面有类型会推断出来
	// var s = []int{1,2,3,4,5,6,7,8}

	fmt.Println(s)
	for index, value := range s {
		fmt.Println(index, value)
	}
	// 0 1
	// 1 2
	// 2 3
	// 3 4
	// 4 5
	// 5 6
	// 6 7
	// 7 8
}
```

### map

The zero value of a map is nil. A nil map has no keys, nor can keys be added.

The make function returns a map of the given type, initialized and ready for use.

### mutate maps

Insert or update an element in map m:

```go
m[key] = elem
```

delete item

```go
delete(m,key)
```

contains check

```go
elem, ok = m[key]
```

1. 如果key 存在m 中，ok 是true，否则ok 是false
2. 如果key 不存在m 中，elem 是0
3. elem 和ok 可能需要在前面预告定义，或者可以用`elem, ok := m[key]`

### function values

Functions are values too. They can be passed around just like other values.

Function values may be used as function arguments and return values.

```go
func multi(x, y int) int{
    return x * y
}
func add(x, y int) int {
    return x + y
}
func compute(fn func(x, y int) int, a, b int) int {
    return fn(a, b)
}
func main(){

    fmt.Println(multi) // 0x10a6a40
    a, b := 1, 20
    var result = compute(add, a,b)
    fmt.Println(result) // 21
    var result = compute(multi, a,b)
    fmt.Println(result) // 20
}
```

### function closures

Go functions may be closures. A closure is a function value that references variables from outside its body. The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.

For example, the adder function returns a closure. Each closure is bound to its own sum variable.

```go
func point() func(x int) int {

	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {

	po1 := point()
	po2 := point()

	for i := 0; i < 10; i++ {
		fmt.Print(po1(i))
		fmt.Print(" ")
		fmt.Println(po2(-i))
	}
}
```

### 中间跳过几章interface的

### stringers

```go
func (ip IPAddr) String() string{
	var s = ip[:]
	var new_s =[]string{}
	for _, value := range s{
		new_s = append(new_s, strconv.Itoa(int(value)))
		new_s = append(new_s, ".")
	}

    var a = ""
    for index, value := range new_s{
        if index < 7{
            a += value
        }
    }
	return a
}

func main() {
	hosts := map[string]IPAddr{
		"loopback":  {127, 0, 0, 1},
		"googleDNS": {8, 8, 8, 8},
	}
	for name, ip := range hosts {
		fmt.Printf("%v: %v\n", name, ip)
	}
}
```

### errors

### goroutine

A goroutine is a lightweight thread managed by the Go runtime.

`go f(x, y, z)`
starts a new goroutine running

`f(x, y, z)`

The evaluation of f, x, y, and z happens in the current goroutine and the execution of f happens in the new goroutine.

Goroutines run in the same address space, so access to shared memory must be synchronized. The sync package provides useful primitives, although you won't need them much in Go as there are other primitives. (See the next slide.)

### channels

Channels are a typed conduit through which you can send and receive values with the channel operator, `<-`.

```go
ch <- v    // Send v to channel ch.
v := <-ch  // Receive from ch, and
           // assign value to v.
```
(The data flows in the direction of the arrow.)

Like maps and slices, channels must be created before use:

```go
ch := make(chan int)
```
By default, sends and receives block until the other side is ready. This allows goroutines to synchronize without explicit locks or condition variables.

The example code sums the numbers in a slice, distributing the work between two goroutines. Once both goroutines have completed their computation, it calculates the final result.

```go
package main

import "fmt"

func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
	c <- sum // send sum to c
}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}
	fmt.Println(s[:len(s)/2])
	fmt.Println(s[len(s)/2:])
	c := make(chan int)
	go sum(s[:len(s)/2], c)
	go sum(s[len(s)/2:], c)
	x, y := <-c, <-c // receive from c

	fmt.Println(x, y, x+y)
}
```

例子2

```go
func send(c chan int, v int) {
    c<-v
}

func main(){

    var c = make(chan int)

    a := 1

    // 不能写成  go c<-a 必须要放到一个方法里
    // 否则报错expression in go must be function call
    go send(c, a)

    v := <-c

    fmt.Println(v)
}
```

### buffered channel

Channels can be buffered. Provide the buffer length as the second argument to make to initialize a buffered channel:

```go
ch := make(chan int, 100)
```
Sends to a buffered channel block only when the buffer is full. Receives block when the buffer is empty.

Modify the example to overfill the buffer and see what happens.

1. 满了时候才发送，空的时候才接收

```go
c = make(chan int, 2)
c <- 1
c <- 2
//c <- 3 // fatal error: all goroutines are asleep - deadlock!
fmt.Println(<-c)
fmt.Println(<-c)
```

### range and close

```go
v, ok := <-ch
```
ok is false if there are no more values to receive and the channel is closed.

The loop for i := range c receives values from the channel repeatedly until it is closed.

### select 

The select statement lets a goroutine wait on multiple communication operations.

A select blocks until one of its cases can run, then it executes that case. It chooses one at random if multiple are ready.

```go
func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {

		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
        quit <- 0

	}()
	fibonacci(c, quit)
}
```

### sync.Mutex

```go
// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	c.v[key]++
	c.mux.Unlock()
}

// Value returns the current value of the counter for the given key.
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	defer c.mux.Unlock()
	return c.v[key]
}
```

### go docker
[原文](http://www.iamlintao.com/6748.html)

```sh
➜  5 docker search golang
NAME                             DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
golang                           Go (golang) is a general purpose, higher-lev…   3347                [OK]
ilanyu/golang-reverseproxy       golang-ReverseProxy                             88                                      [OK]
...

➜  5 docker pull golang
Using default tag: latest
latest: Pulling from library/golang
57df1a1f1ad8: Pull complete
71e126169501: Pull complete
1af28a55c3f3: Pull complete
...


➜  nethttp cat Dockerfile
## 引入最新的golan ，不设置版本即为最新版本
FROM golang
## 在docker的根目录下创建相应的使用目录
RUN mkdir -p /www/webapp
## 设置工作目录
WORKDIR /www/webapp
## 把当前（宿主机上）目录下的文件都复制到docker上刚创建的目录下
COPY . /www/webapp
## 编译
RUN go build .
## 设置docker要开发的哪个端口
EXPOSE 8080
## 启动docker需要执行的文件
CMD go run main.go


➜  nethttp l
total 16
drwxr-xr-x  4 wuxiaoming  staff   128B  9 17 10:30 .
drwxr-xr-x  3 wuxiaoming  staff    96B  9 17 10:23 ..
-rw-r--r--  1 wuxiaoming  staff   438B  9 17 10:30 Dockerfile
-rw-r--r--  1 wuxiaoming  staff   193B  9 17 10:28 main.go
➜  nethttp docker image build -t goweb .
Sending build context to Docker daemon  3.072kB
Step 1/7 : FROM golang
 ---> 05c8f6d2538a
Step 2/7 : RUN mkdir -p /www/webapp
 ---> Using cache
 ---> f4a926db5219
Step 3/7 : WORKDIR /www/webapp
 ---> Using cache
 ---> 718f6906479a
Step 4/7 : COPY . /www/webapp
 ---> dc99973dd5e9
Step 5/7 : RUN go build .
 ---> Running in a03e6c38bb28
Removing intermediate container a03e6c38bb28
 ---> 4f9470381745
Step 6/7 : EXPOSE 8080
 ---> Running in 0a79abb8e00a
Removing intermediate container 0a79abb8e00a
 ---> e5405ffaaeec
Step 7/7 : CMD go run main.go
 ---> Running in 54684fcdb828
Removing intermediate container 54684fcdb828
 ---> 521e41f5e94e
Successfully built 521e41f5e94e
Successfully tagged goweb:latest

➜  nethttp docker images
REPOSITORY                                      TAG                 IMAGE ID            CREATED              SIZE
goweb                                           latest              521e41f5e94e        51 seconds ago       841MB
golang                                          latest              05c8f6d2538a        6 days ago           839MB
...
➜  nethttp docker run goweb
# docker run -d --name golang -p 8080:8080 --net mynet --ip 172.172.0.10 -v /Volumes/work/www/Go/webapp/:/www/webapp goweb
x < 100
 x < 5
```

### gin

```go
// main 
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```
```sh
➜  test1 go run main.go
main.go:3:8: cannot find module providing package github.com/gin-gonic/gin: working directory is not part of a module
➜  test1 go mod init main
go: creating new go.mod: module main
➜  test1 go run main.go
go: finding module for package github.com/gin-gonic/gin
go: found github.com/gin-gonic/gin in github.com/gin-gonic/gin v1.6.3
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:	export GIN_MODE=release
 - using code:	gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /ping                     --> main.main.func1 (3 handlers)
[GIN-debug] Environment variable PORT is undefined. Using port :8080 by default
[GIN-debug] Listening and serving HTTP on :8080
[GIN] 2020/09/17 - 10:48:34 | 404 |       1.328µs |             ::1 | GET      "/"
[GIN] 2020/09/17 - 10:48:34 | 404 |         663ns |             ::1 | GET      "/favicon.ico"
[GIN] 2020/09/17 - 10:48:44 | 200 |      65.201µs |             ::1 | GET      "/ping"
```
