# Golang

life is short, I use golang

## Install

`https://studygolang.com/dl`

1. 以Linux为例子，首先下载安装包`tar.gz`到`/home/`目录下

2. 解压，添加环境变量

   ~~~shell
   export PATH=$PATH:/home/go/bin
   ~~~

   为了避免每次都要添加变量，可以把上面这句添加到`.bashrc`的末尾，这样bash在每次启动的时候都会加载这个文件的内容

3. 验证是否安装成功

   `go version`
   如果成功的话可以看到`go version go1.14.1 windows/amd64`

## Compile

一个go程序要运行起来，需要我们掌握一些简单的编译工具

1. go run命令

   `go run HelloGo.go`

2. go build命令

   `go build HelloGo.go`

   可使用`-o`参数来指定生成的可执行文件的名称

   `go build -o Hello HelloGo.go`

## Usage

### 变量的声明

变量是允许改变的，与之相对的是标量，当我们声明一个变量后，Go语言会把变量对应的内存区域进行初始化。

> note：每个变量在被声明时都会被初始化为该类型对应的默认值，每个变量必须被使用，否则编译不通过

- 一次声明一个变量

  ~~~go
  var input string
  var a int 
  var b float
  var list string[]
  var st struct{           // 声明一个匿名结构体，结构体内有一个int类型的字段
      x int
  }
  var f func() bool        // 声明一个函数变量
  ~~~

- 一次声明多个变量

  ~~~go
  var (
  	f int
      g string
  )
  ~~~

- 声明变量并赋值

  `var a int = 100`

  当然也可以使用Go语言提供的语法糖，精简为一下格式

  `a := 100`

  > `:=`左边必须至少有一个变量是要新建的，否则编译会报错。另外这个语法糖执行过程中，编译器会

- 简单赋值

  声明过的变量可以在命名空间内进行值的变更

  ~~~go
  a := 100
  a = 500
  // 类似python的换值操作，前提是两者必须类型一样
  b := 200
  a, b = b, a
  ~~~

- 使用匿名变量

  有些函数的返回值你不需要用，可以不用专门分配一个变量去接收，直接用匿名变量不会占用内存空间

  ~~~go
  ...
  surname, _ := getName()
  ~~~

### 原生的数据类型

基本类型有：整型，浮点数，布尔型，字符串型。
由这些基本元素又可以组成他们的集合类型：切片，结构体，指针，通道，map和数组等

- #### 整型

  1. 按照长度：int8，int16，int32，int64
  2. 按照正负：uint8，uint16，uint32，uint64

  整型之间可以互相转换，高长度类型向低长度类型转换会发生长度截取，金辉保留高长度类型的低位值，造成转换错误

- #### 浮点型

  1. float32
  2. float64

  打印浮点数精度跟C语言一样，可以配合%f使用

  ~~~go
  fmt.Printf("%f\n",math.E)  //按照默认宽度和精度输出
  fmt.Printf("%.2f\n",math.E)  //按照默认宽度和2位精度输出
  ~~~

- #### 布尔型

  true 和 false

  > Go语言的布尔型不可以与整行进行强制转换，也无法参与数值运算

- #### 字符串类型

  基于`utf8`编码实现，所以在遍历字符串类型时，需要区分`byte`和`rune`

  ~~~go
  f := "Golang编程"
  fmt.Printf("len is %v", len(f))  // len is 12
  fmt.Printf("len is %v", utf8.RuneCountInString(f))  // len is 8
  ~~~

  第一种方式是统计byte的长度，类型uint8，代表了有多少个独立的ASCII字符，一个中文占3位。

  第二种是统计得到的是rune的长度，类型是int32，代表了有多少个UTF8字符，一个中文只占1位。

### 指针

Go语言提供的自动垃圾回收机制，减少了对指针占用内存回收的复杂性

指针包含三个概念

1. 指针地址
2. 指针类型
3. 指针取值

> 程序运行时，每一个变量的值都保存在内存中，变量所在的内存由固定的地址，这个在创建变量的时候就已经分配好，就算变量的值再怎么改变，这个变量的内存地址都不会改变。

- #### 取变量的指针：`&`

  假设有一个string变量T，我们可以通过取址符号&获取这个变量对应的内存地址，生成变量T的指针。
  **变量的内存地址**就是**指针的值**

  变量是`T`，类型是`string`，值是你赋予的那串具体字符串

  指针是`&T`，类型是`*string`，值是一个Go语言分配的内存地址

- #### 取指针对应的变量的值：`*`

  `*`除了能用来表示指针类型，他也是一个取指针对应的变量的值的操作

  ~~~go
  package main
  
  import "fmt"
  
  func main() {
  	T := "hello"
  	T1 := &T
  	T2 := *T1
  	fmt.Printf("Type is %T, Value is %v, Address is %p\n", T, T, &T)	
  	fmt.Printf("Type is %T, Value is %v, Address is %p\n", T1, T1, &T1)	
  	fmt.Printf("Type is %T, Value is %v, Address is %p\n", T2, T2, &T2)	
  }
  ~~~

  ~~~shell
  (base) C:\Users\yunshuo\Desktop>go run hello.go
  Type is string, Value is hello, Address is 0xc0000401f0
  Type is *string, Value is 0xc0000401f0, Address is 0xc000006028
  Type is string, Value is hello, Address is 0xc000040200
  ~~~

- #### 直接创建指针：`new()`

  指针的生成可以是用`&`对变量进行取址，也可以直接使用new方法创建，
  新**创建的指针必须声明想要作为什么基本类型的指针**，比如想要作string的指针

  ~~~go
  str := new(string)  // str 变量实际是一个指针类型，值是一个内存地址
  *str = "Golang id Good"  // *str是一个string类型，通过指针进行赋值
  ~~~

  > 可以对指针的值进行继续取指针操作，但是不能`&&`一个变量
  >
  > 取值`*`只能用在指针类型上，用在变量上会报错`invalid indirect of T (type string)`