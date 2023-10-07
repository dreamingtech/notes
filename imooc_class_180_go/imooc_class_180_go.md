go 语言工具链 国内镜像, 第三方依赖管理

go github pull request 数量统计
https://madnight.github.io/githut/#/pull_requests/2023/1

区块链职位要求最多的top15技能
https://www.sohu.com/a/254366823_100111840

开发快, 运行快, 不负债

知乎社区核心业务 Golang 化实践
https://zhuanlan.zhihu.com/p/48039838
https://www.informerug.com/?p=66001
https://github.com/awesee/books/tree/main/Go/Gopher%20China%202019
https://github.com/awesee

猜画小歌 Google AI 

https://trends.google.com/trends/explore?q=%2Fm%2F09gbxjr

七牛云 beego

Caddy CockroachDB



- 现有的 go 语言课程
  - 罗列语法
  - 为语法而举例

- 学习 go 语言很简单, 因为语法简单
- 学好 go 语言不简单, 因为要调整三观

- 精心挑选的示例
  - 语法部分:  每个小片段解决实际问题
  - 综合部分:  经典算法, 典型例题, 微型项目
  - 实战部分:  搭建分布式爬虫, 结构复杂

其它语言的示例
与其它语言的比较
深入分析设计者背后的思想和取舍
触类旁通, 对自己已经掌握的语言有新的认识

为转型而设计的课程

- 课程概览
  - 基本语法
    - 变量
    - 选择
    - 循环
    - 指针, 数组, 容器
  - 面向接口编程
    - 结构体
    - duck typing 的概念
    - 组合的思想
  - 函数式编程
    - 闭包的概念
    - 多样的例题
  - 工程化
    - 资源管理, 错误处理
    - 测试和文档
    - 性能调优
  - 并发编程
    - goroutine 和 channel
    - 理解调度器
    - 多样的例题
  - 实战项目
    - 从 0 开始搭建简单分布式爬虫
    - 相亲网站及数据分析
    - 功能扩充: 抓取汽车网站

实战项目的实现步骤

单任务版 > 并发版 > 分布式



## 开发环境

https://studygolang.com/
https://go.dev/



go版本大于 1.13

```shell
go version
# go version go1.19.1 linux/amd64

go env
# GO111MODULE=""
# GOARCH="amd64"
# GOBIN=""
# GOCACHE="/home/ubuntu/.cache/go-build"
# GOENV="/home/ubuntu/.config/go/env"
# GOEXE=""
# GOEXPERIMENT=""
# GOFLAGS=""
# GOHOSTARCH="amd64"
# GOHOSTOS="linux"
# GOINSECURE=""
# GOMODCACHE="/home/ubuntu/go/pkg/mod"
# GONOPROXY=""
# GONOSUMDB=""
# GOOS="linux"
# GOPATH="/home/ubuntu/go"
# GOPRIVATE=""
# GOPROXY="https://proxy.golang.org,direct"
# GOROOT="/usr/local/go"
# GOSUMDB="sum.golang.org"
# GOTMPDIR=""
# GOTOOLDIR="/usr/local/go/pkg/tool/linux_amd64"
# GOVCS=""
# GOVERSION="go1.19.1"
# GCCGO="gccgo"
# GOAMD64="v1"
# AR="ar"
# CC="gcc"
# CXX="g++"
# CGO_ENABLED="1"
# GOMOD="/dev/null"
# GOWORK=""
# CGO_CFLAGS="-g -O2"
# CGO_CPPFLAGS=""
# CGO_CXXFLAGS="-g -O2"
# CGO_FFLAGS="-g -O2"
# CGO_LDFLAGS="-g -O2"
# PKG_CONFIG="pkg-config"
# GOGCCFLAGS="-fPIC -m64 -pthread -Wl,--no-gc-sections -fmessage-length=0 -fdebug-prefix-map=/tmp/go-build3956028623=/tmp/go-build -gno-record-gcc-switches"

```
GO111MODULE 默认为 "", 表示的含义: 

如果工作目录在 GOPATH 下, GO111MODULE 为 off, 

如果工作目录不在 GOPATH 下, GO111MODULE 为 on 

### 设置国内代理

https://github.com/goproxy/goproxy.cn
https://goproxy.cn/

```shell

# 设置代理, 拉取依赖时, 如 github 库时, 先从 proxy.golang.org 查询, 
# 如果未找到, 再 direct 查询官方库
GOPROXY="https://proxy.golang.org,direct"

# 国内镜像 goproxy.cn

```

[Go 1.13 及以上（推荐）](https://goproxy.cn/#usage-go-113-and-above-recommended)

打开你的终端并执行

```shell

$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn,direct

```

[macOS 或 Linux](https://goproxy.cn/#usage-macos-or-linux)

打开你的终端并执行

```shell

$ export GO111MODULE=on
$ export GOPROXY=https://goproxy.cn

```

或者

```shell

$ echo "export GO111MODULE=on" >> ~/.profile
$ echo "export GOPROXY=https://goproxy.cn" >> ~/.profile
$ source ~/.profile

```

[Windows](https://goproxy.cn/#usage-windows)

打开你的 PowerShell 并执行

```shell

C:\> $env:GO111MODULE = "on"
C:\> $env:GOPROXY = "https://goproxy.cn"

```

或者

```

1. 打开“开始”并搜索“env”
2. 选择“编辑系统环境变量”
3. 点击“环境变量…”按钮
4. 在“<你的用户名> 的用户变量”章节下（上半部分）
5. 点击“新建…”按钮
6. 选择“变量名”输入框并输入“GO111MODULE”
7. 选择“变量值”输入框并输入“on”
8. 点击“确定”按钮
9. 点击“新建…”按钮
10. 选择“变量名”输入框并输入“GOPROXY”
11. 选择“变量值”输入框并输入“https://goproxy.cn”
12. 点击“确定”按钮

```

### 关于 GO111MODULE 和 GOPATH

- 逐渐淡化 GOPATH, 而是使用 GO111MODULE

GO111MODULE=""
GOPATH="/home/ubuntu/go"

```shell

go env -w GO111MODULE=on

```



- 把on打成ON的同学看这里

这里我们的on必须是小写的, 不是大写ON, 也不是1或者true等. 打错的同学就会碰到这个bug: https://github.com/golang/go/issues/34880 它不会告诉你打错了, 但是接下来go env, 甚至其它go run, go install等都不能用了. 

出错信息是: 

```shell

root@d952a1d8fe21:/go# go env
go: unknown environment setting GO111MODULE=ON

```

这样解决: 

```shell

root@d952a1d8fe21:/go# export GO111MODULE=on    # 设置环境变量, windows的同学使用SET GO111MODULE=on
root@d952a1d8fe21:/go# go env -w GO111MODULE=on  # 重新向go env写入正确的值
root@d952a1d8fe21:/go# go env   # 验证问题解决
GO111MODULE="on"
GOARCH="amd64"
GOBIN=""
GOCACHE="/root/.cache/go-build"
GOENV="/root/.config/go/env"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"

```


- goimports 工具

设置好国内镜像后, 就可以方便的安装了

```shell

go get -v golang.org/x/tools/cmd/goimports

# go: go.mod file not found in current directory or any parent directory.
#         'go get' is no longer supported outside a module.
#         To build and install a command, use 'go install' with a version,
#         like 'go install example.com/cmd@latest'
#         For more information, see https://golang.org/doc/go-get-install-deprecation
#         or run 'go help get' or 'go help install'.

```

- idea 使用 go

  - 安装插件 go, 

  - 安装插件 file watchers 保存文件时会自动运行格式化工具对代码进行格式化

- goland 配置
  - go module
  - parameter hint
  - goimports

新建项目并初始化

```shell
cd imooc_class_180_go\code_study\learngo

go mod init learngo
# go: creating new go.mod: module learngo

cat go.mod
# module learngo
# 
# go 1.19
```



```shell
go mod init github.com/dreamingtech/learngo
# go: creating new go.mod: module github.com/dreamingtech/learngo

cat go.mod
# module github.com/dreamingtech/learngo
# 
# go 1.19

```

module 类似于其它语言的 package


vscode 安装配置


```go
// hello.go

package main

import (
    "fmt"
    "runtime"
)

// 变量的零值
// 变量名在前, 类型在后, 在定义复合变量类型时会更加方便
func variableZeroValue() {
    var a int
    var s string
    fmt.Println(a, s)
    // 打印出空字符串
    // q 表示 quotation, 引用
    fmt.Printf("%d %q\n", a, s)
}

// 变量初始化时赋值
func variableInitialValue() {
    var a, b int = 3, 4
    var s string = "abc"
    fmt.Println(a, b, s)
}

// 省略变量类型, 变量类型自动推断
func variableTypeDeduction() {
    var a, b, c = 3, 4, true
    var s = "abc"
    fmt.Println(a, b, c, s)
}

// 类型推断缩略形式
func variableShorten() {
    // 缩略形式的类型推断只能在函数中使用
    a, b, c := 3, 4, true
    b = 5
    s := "abc"
    fmt.Println(a, b, c, s)
}

// 全局变量的作用域是整个包
// 函数外定义变量时不能使用缩略形式的类型推断
var aa = 3
var bb = true
var ss = "ss"

// 一个 var 定义多个变量
var (
    aaa = 33
    bbb = true
    sss = "sss"
)

func main() {
    fmt.Println("hello world")
    fmt.Println(runtime.GOARCH)
    variableZeroValue()
    variableInitialValue()
    variableTypeDeduction()
    println(aa, bb, ss, aaa, bbb, sss)
}

```

## 基础语法

[01｜知识回顾: Go基础知识你真的掌握了吗?  (geekbang.com)](https://time.geekbang.com/column/article/579737)

### 变量与类型

#### 变量的声明与赋值

特别是在 Go 函数中使用相当频繁的变量赋值语句 := , 其将在编译时对类型进行自动推断. 

#### Go 中的内置类型

```

int  int8  int16  int32  int64
uint  uint8  uint16  uint32  uint64  uintptr
float32  float64  complex128  complex64
bool  byte  rune  string

```

bool
string
int, uint 不指定长度的 int, 如果操作系统是 32 位的, 等价于 int32, 64位系统中, 等价于 int64
int8, uint8
int16, uint16
int32, uint32
int64, uint64
uintptr 指针, 长度也是随着操作系统而变
byte 8位, 1字节, 相当于 int8 的别名

rune 字符型, 32位, 4字节, 相当于 int32 的别名
相当于其它变量的 char, char 默认是1字节, 但在国际化字符编码中可能会带来很多问题, 
unicode 是2字节, 使用 utf8 编码时, 有的字符为 3 字节. 所以使用 4 字节的 rune 来表示字符型变量

float32
float64
complex64 实部和虚部分别是 32 位
complex64
complex128 实部和虚部分别是 64 位
complex128


```go
// ch02-datatype/class01-basic/basic.go

package main

import (
    "fmt"
    "math"
    "math/cmplx"
    "runtime"
)

func variableZeroValue() {
    var a int
    var s string
    fmt.Println(a, s)
    // 打印出空字符串
    fmt.Printf("%d %q\n", a, s)
}

func variableInitialValue() {
    var a, b int = 3, 4
    var s string = "abc"
    fmt.Println(a, b, s)
}

func variableTypeDeduction() {
    // 使用 var 定义变量时可以不指定类型, 由编译器自动推断
    var a, b, c = 3, 4, true
    var s = "abc"
    fmt.Println(a, b, c, s)
}

// 全局变量的作用域是整个包
var aa = 3
var bb = true
var ss = "ss"

// 一个 var 定义多个变量 (推荐)
var (
    aaa = 33
    bbb = true
    sss = "sss"
)

func variableShorten() {
    // := 只能用于函数内部, 不能用于全局变量
    a, b, c := 3, 4, true
    b = 5
    s := "abc"
    fmt.Println(a, b, c, s)
}

// 欧拉公式
func euler() {
    // 定义复数
    c := 3 + 4i
    // cmplx.Abs() 计算复数的模
    fmt.Println(cmplx.Abs(c))
    // 欧拉公式 e^iπ + 1 = 0
    // (0+1.2246467991473515e-16i)
    // i 单独写时会被识别为变量, 必须使用 1i 才能表示为 虚数 i
    c2 := cmplx.Pow(math.E, 1i*math.Pi) + 1
    fmt.Println(c2)
    // 以 e 为底的指数函数
    c3 := cmplx.Exp(1i*math.Pi) + 1
    // (0+1.2246467991473515e-16i)
    fmt.Println(c3)
    // 保留三位小数 %.3f (0.000+0.000i)
    fmt.Printf("%.3f\n", c3)
}

func main() {
    fmt.Println("hello world")
    fmt.Println(runtime.GOARCH)
    variableZeroValue()
    variableInitialValue()
    variableTypeDeduction()
    variableShorten()
    println(aa, bb, ss, aaa, bbb, sss)
    euler()
}

```

问题: 复数可以用在什么场合
https://coding.imooc.com/learn/questiondetail/2vGBE6xWpdlXNkaq.html

复数就用在需要使用复数的场合, 业务逻辑开发方面的确用不到. 最通俗的例子, 就是信号处理里的fft了. 

```python
# -*- coding: utf-8 -*-

import math
import cmath

# TypeError: can't convert complex to float
# print(math.exp(1j + math.pi) + 1)
# (13.502969588876512+19.472221418841606j)
print(cmath.exp(1j + math.pi) + 1)

```



##### 强制类型转换

go 只有强制类型转换, 没有隐式类型转换

```go

// ch02-datatype/class02-cast/cast.go

package main

import (
    "fmt"
    "math"
)

// 计算直角三角形的斜边长度
func triangle() {
    var a, b int = 3, 4
    var c int
    // 不能直接使用 math.Sqrt(a*a + b*b), 而是要进行类型转换
    // var c int = math.Sqrt(a*a + b*b)
    // func Sqrt(x float64) float64 {}
    // 强制类型转换
    c = int(math.Sqrt(float64(a*a + b*b)))
    fmt.Println(c)
    dd := a*a + b*b
    // dd = 25, type: int
    fmt.Printf("dd = %d, type: %T", dd, dd)
}

func main() {
    triangle()
}

```

问题: 浮点数及转整数的问题

在计算其它边长的结果时, 可能无法得到正确的数值, 这时因为浮点数转整数引起的, 如
6, 6 > 8
5, 6 > 7

https://coding.imooc.com/learn/questiondetail/9eAQxP2yaNR60BjG.html

- 解决问题的方法

  - 1. 浮点数转换为整数的问题

浮点数转换为整数时, 只会取整数部分, 会忽略所有的小数, 如 4.9999 会被转换成 4 而不是 5. 

  - 2. 浮点数不能精确表示一些数

浮点数也是有一定精度范围的, 不能精确表示所有的数, 比如, 它有可能没有办法精确的表示 3 开根号

  - 总结

由于浮点数本身无法精确表示一个数, 所以这种问题并没有一个通用的或完美的解决办法, 而是要具体问题具体分析. 

如在这里我们知道斜边 5 一定是整数, 所以把结果四舍五入, 用 math.Round, 然后再转整数就可以了. 

在精度要求高的场合, 我们采用定点数, 比如 go 语言有 big.Float


问题: 对于很小的数的处理

https://coding.imooc.com/learn/questiondetail/y82Qv6A8wQQXd5Nn.html

在比较两个数是否相等时, 一般来说, 我们可以设一个很小的值, eps, epsillon 的简称, 如果两个数差的绝对值小于它, 就算相等. 

如果计算结果确实是一个非常小的小数, 没关系, 浮点数里“浮”的意思是它并不是说精度一定多少, 而是有效数字一共多少.  

如果我们处理的数据都很小的话, 那么这个 eps 设的更小就行了. 但这样可能不太直观, 那我们可以改变一下单位, 使用比如纳米, 数字就没那么小了. 

模拟物理定律可能通常对精度要求没那么高, 用浮点数即可. 

但设计其它场合比如金融, 算钱的话就不适合用浮点数了. 可以用整数, 比如1代表0.01分. 或者用高精度数, 定点数, 有理数等. 可以参考 math/big 库, 里面有 big.Int, big.Float, big.Rat

问题: 数学很重要吗, 学习golang

https://coding.imooc.com/learn/questiondetail/2jRAkYdk7J9Pwn5m.html

数学分支很多. 逻辑本身就是数学的一个分支. 除此之外, 组合数学也非常重要, 但通常我们学好数据结构和算法就够了, 当然更为推荐的是从理论上来学习组合数学. 这些就是一般后端go语言开发者需要的数学了. 

另一个重要分支是线性代数, 这在图形学和机器学习都是必须, 做前端其实最好也有些这方面的基础. 

相反高等数学不是必须, 行业软件, 游戏引擎, 金融等领域的开发需要. 不过仍然建议学好高等数学, 毕竟思维能力会上一个台阶. 

##### 问题: 类型转换有没有类似js的parseInt的方法
https://coding.imooc.com/learn/questiondetail/Ggzqw6jwDA8PDxW0.html

JavaScript 举例

```js
let a = "1";
b = parseInt(a);
console.log(isNaN(b));

// 结果
// false
```

Golang 举例

```go

a := "1"
b = int(a);
fmt.Println(b);

结果
error: cannot convert a (type string) to type int

```
strconv.ParseInt
strconv.Atoi(a)



#### 变量的命名规则

#### 变量的生命周期

需要了解变量何时存在, 何时消亡. 

#### 变量的作用域

Go 的词法范围使用花括号{…}作为分割. 根据作用域的范围大小, 可以分为全局作用域、包作用域、文件作用域、函数作用域. 

### 表达式与运算符

算术运算符；

关系运算符；

逻辑运算符；

位运算符；

赋值运算符；

地址运算符. 



#### 运算符优先级



```

  优先级(由高到低)              操作符
    5                *  /  %  <<  >>  &  &^
    4                +  -  |  ^
    3                ==  !=  <  <=  >  >=
    2                &&
    1                ||
```



### 基本控制结构

程序并不都是一行一行顺序执行的, 还可能根据条件跳转到其他语句执行, 这就涉及到基本控制结构了. 理论和实践表明, 无论多复杂的算法, 都可以通过顺序、选择、循环 3 种基本控制结构构造出来. 

下面给出 Go 语言基本控制结构的几种形式: 

if else 语句；

```go

if{

}else if {

}else {

}
```

switch 语句；

```go

switch var1 {
    case val1:
        ...
    case val2,val3:
        ...
    default:
        ...
}
```



### 4 种 for 循环语句

1）完整的 C 风格的 for 循环

```go

for i := 0; i < 10; i++ {
    fmt.Println(i)
}

```



2）只有条件判断的 for 循环

```go

i := 1
for i < 100 {
        fmt.Println(i)
        i = i * 2
}
```



### 函数

高级语言都离不开函数这个概念. 通过函数, 我们可以给一连串的复合操作定义一个名字, 把它们作为一个操作单元. 函数是一种定义过程的强大抽象技术, 它可以帮助我们构建大规模的程序. 在 Go 语言中, 函数也具有一些和其他语言不太一样的特性, 例如函数是一等公民. 对于函数的基本用法, 需要掌握: 

#### 基本的函数声明

```go

func name(parameter-list) (result-list) {
    body
}

```

#### 函数的多返回值特性

```go

func div (a,b int) (int,error){
    if b == 0 {
     return 0, errors.New("b cat't be 0")
    }
    return a/b,nil
}
```



#### 可变参数函数

```go

func Println(a ...interface{}) (n int, err error)
```



#### 递归函数

```go

func f(n int) int {
  if n == 1 {
    return 1
  }
  return n * f(n-1)
}
```



#### 函数作作为参数

函数作为一等公民拥有一些灵活的特性. 

函数作为参数时, 可以提升程序的扩展性. 

```go

package main
import (
    "fmt"
)
// 遍历切片的每个元素, 通过给定函数进行元素访问
func visit(list []int, f func(int)) {
    for _, v := range list {
        f(v)
    }
}

func main() {
    // 使用匿名函数打印切片内容
    visit([]int{1, 2, 3, 4}, func(v int) {
        fmt.Println(v)
    })
}
```

#### 函数作为返回值

函数作为返回值时, 一般在闭包和构建功能中间件时使用得比较多, 在不修改过去核心代码的基础上, 用比较小的代价增加了新的功能. 

```go

func logging(f http.HandlerFunc) http.HandlerFunc{
  return func(w http.ResponseWriter, r *http.Request) {
    log.Println(r.URL.Path)
    f(w,r)
  }
}
```



#### 函数作为值

```go

var opMap = map[string]func(int, int) int{
    "+": add,
    "-": sub,
    "*": mul,
    "/": div,
}

f := opMap[op]
f()
```

#### 值传递还是引用传递

最后要强调的是, Go 函数调用时参数是值传递, 在调用过程中修改函数参数不会影响到原始的值. 

在 Go 语言中, 指针、切片、字典（map）、接口、函数以及通道（channel）都是引用类型. 因此在传递这些类型的参数时, 传递的是参数的指针, 而不是参数的副本. 这意味着, 在函数内部对这些类型的参数的修改会影响到原来的参数. 

### 复合数据类型

高级程序语言帮助我们把简单类型组合起来, 形成了复合类型. 复合类型将我们对程序设计的抽象提到一个新的高度. 它增加了程序的模块化程度, 并增强了语言的表达能力. 例如, 我们要处理分数. 分数有分子和分母之分, 如果我们只有基础的数据类型, 这种处理将变得繁琐. 而如果有了复合类型. 我们就可以将分子和分母看做一个整体了. 

为了形成复合数据, 编程语言应该提供某种“胶水”, 方便将数据对象组合起来, 形成更复杂的数据对象. 复合类型正是这样的胶水, 它让程序更易于设计、维护和修改. 

Go 语言中内置的复合类型包括: 数组、切片、哈希表, 以及用户自定义的结构体. 

#### 数组



#### 切片

相比于数组, 在 Go 语言中使用最多的是切片. 切片基础语法需要掌握下面这些内容. 



#### 哈希表



#### 结构体






### fmt.Printf

`fmt.Printf` supports several format verbs that can be used to format values of different types:

- `%v` - prints the value in its default format
- `%+v` - prints the value in its default format, with additional information such as field names for structs
- `%#v` - prints the value in a Go-syntax format
- `%T` - prints the type of the value
- `%t` - prints a boolean value as "true" or "false"
- `%d` - prints an integer in base 10
- `%o` - prints an integer in base 8
- `%x` - prints an integer in base 16, with lowercase letters for a-f
- `%X` - prints an integer in base 16, with uppercase letters for A-F
- `%b` - prints an integer in base 2
- `%c` - prints the Unicode character corresponding to the given integer
- `%s` - prints a string
- `%q` - prints a quoted string, safely escaped for use in Go source code
- `%p` - prints a pointer address in hexadecimal notation

These format verbs can be combined with other formatting options, such as width and precision, to create more complex output. For more information, refer to the documentation for the `fmt` package in Go.



- 变量定义要点回顾

  - 变量定义要点回顾
  - 变量类型写在变量名之后
  - 编译器可推导变量类型
  - 没有char, 只有rune
  - 原生支持复数类型

```go
// ch02-datatype/class03-const/const.go
package main

import (
    "fmt"
    "math"
)

func consts() {
    // 定义常量, 不能使用 :=, 只能使用 const
    // 常量的值在编译时就确定了, 不能使用 := 定义
    // 常量的值不能是函数的返回值
    // 常量的值必须是编译期可以确定的值, 不能是函数的返回值
    // 常量的值必须是数字, 字符串, 布尔值
    // 常量的值可以是任意类型的值, 但是常量的类型只能是布尔型, 数字型(整数型, 浮点型, 复数), 字符串型
    // 常量的值不能是变量
    // go 语言中的常量是不占用内存的
    // go 语言中的常量是不允许修改的
    // go 语言中的常量是不允许取地址的
    // go 语言中的常量是不允许使用 new() 函数的
    // go 语言中的常量是不允许使用 make() 函数的
    // go 语言中的常量一般不全部大写, 一般使用驼峰命名法
    const filename = "abc.txt"
    const a, b = 3, 4
    var c int
    // 没有指定常量的类型时, 计算时会自动转换为相应的类型, 这里计算时会自动把 a 和 b 转换为 float64
    c = int(math.Sqrt(a*a + b*b))
    fmt.Println(filename, c)
    // print type of c
    // c = 5, type: int
    fmt.Printf("c = %d, type: %T\n", c, c)
    fmt.Println("--------------------")

    // 可以使用 const 定义多个常量
    const (
        d, e int = 3, 4
        ff       = 5.5
    )
    var r int
    // 指定常量的类型时, 计算时不会自动转换为相应的类型
    r = int(math.Sqrt(float64(d*d + e*e)))
    fmt.Println(r)
    // print type of ff
    // ff = 5.500000, type: float64
    fmt.Printf("ff = %f, type: %T\n", ff, ff)
}

func enums() {
    // 普通枚举类型
    const (
        cpp        = 0
        java       = 1
        python     = 2
        golang     = 3
        javascript = 4
    )
    // 0 2 3 4 5
    fmt.Println(cpp, java, python, golang, javascript)

    // 自增值枚举类型
    // 使用 iota 定义常量, iota 表示这一组常量是自增值的
    const (
        c = iota
        _ // 跳过一个常量
        j
        p
        g
        js
    )
    // 0 2 3 4 5
    fmt.Println(c, j, p, g, js)

    // iota 可以作为一组自增值的种子, 只要写出来表达式, 就可以基于此定义枚举类型
    const (
        // 1 << 10 = 1024 = 1KB
        // 左移 1 位, 相当乘以 2, 左移 10 位, 相当于乘以 2 的 10 次方, 即 1024
        b = 1 << (10 * iota)
        kb
        mb
        gb
        tb
        pb
    )
    // 1 1024 1048576 1073741824 1099511627776 1125899906842624
    fmt.Println(b, kb, mb, gb, tb, pb)
}

func main() {
    consts()
    enums()
}

```

#### 条件语句

```go
// learngo/ch02-datatype/class04-branch/branch.go

package main

import (
    "fmt"
    "os"
)

func openFile() {
    const filename = "ch02-datatype/class04-branch/abc.txt"
    // https://pkg.go.dev/io/ioutil#ReadFile
    // Deprecated: As of Go 1.16, this function simply calls os.ReadFile.
    contents, err := os.ReadFile(filename)
    if err != nil {
        // open abc.txt: The system cannot find the file specified.
        fmt.Println(err)
    } else {
        // contents 是一个 byte 数组
        fmt.Printf("%s\n", contents)
    }
}

// if 和 error 处理写在一行中
// if 的条件中可以有赋值语句
// if 的条件里赋值的变量的作用域在这个 if 语句里
func openFileError() {
    const filename = "ch02-datatype/class04-branch/abcd.txt"
    // 此时 contents 和 err 为局部变量, 作用域仅限于 if 语句块
    if contents, err := os.ReadFile(filename); err != nil {
        fmt.Println(err)
    } else {
        fmt.Printf("%s\n", contents)
    }
}

func getGrade(score int) string {
    g := ""
    switch {
    case score < 0 || score > 100:
        fmt.Printf("Wrong score: %d", score)
    case score < 60:
        g = "F"
    case score < 80:
        g = "C"
    case score < 90:
        g = "B"
    case score <= 100:
        g = "A"
    }
    return g
}

func calGrade() {
    var grade string
    grade = getGrade(0)
    fmt.Println(grade)
    grade = getGrade(10)
    fmt.Println(grade)
    grade = getGrade(80)
    fmt.Println(grade)
    grade = getGrade(-1)
    fmt.Println(grade)
    grade = getGrade(101)
    fmt.Println(grade)
}

// 根据 op 对传入的两个数进行计算
func eval(a, b int, op string) int {
    switch op {

    }
}

func main() {
    openFile()
    openFileError()
    calGrade()
}

```


## 2-7 指针

是参数传递是值传递还是引用传递?

C, C++ 既可以是值传递又可以是引用传递
Java, Python 除了系统的自建类型, 绝大部分是引用传递, 



```c++

void pass_by_val(int a) {
    a++;
}

void pass_by_ref(int& a) {
    a++;
}

int main() {
    int a = 3;

    pass_by_val(a);
    // After pass_by_val: 3
    printf("After pass_by_val: %d\n", a)

    pass_by_ref(a);
    // After pass_by_val: 4
    printf("After pass_by_ref: %d\n", a)
}

```



Go 语言只有值传递一种方式

### 第一种情况: 变量作为参数传递

拷贝变量进行传递, 函数中的修改不会影响到原来的变量

```go

var a int

func f(a int)

f(a)

```

第二种情况: 变量的指针作为参数传递

此时会把 &a 即 a 的地址拷贝一份传递到函数中, &a 和 pa 都指向 a 的内存地址, 所以函数中的修改会影响到原来的值

即使用指针实现了类似引用传递的效果

```go

var a int
func f(pa *int)
f(&a)

```

第三种情况: 对象/结构体作为参数传递

结构体 cache 一般不携带数据 data, 只是保存了一个指向数据 data 的指针, 会把 cache 整体拷贝一份传递到函数中, 此时 pData 指向的是同一个数据, 所以函数中的修改会影响到原来的值

```go
var cache Cache
func f(cache Cache)
f(cache)

```


所以 go 语言中自定义的数据类型, 在定义时就要考虑到 使用时是以值的方式来使用还是以指针的方式来使用


其它字符串操作

Fields, Split, Join
Contains, Index
ToLower, ToUpper
Trim, TrimRight, TrimLeft

strings 包
strings.Split(s, sep) 按照 sep 分割字符串 s, 返回一个字符串切片
strings.Contains(s, substr) 判断字符串 s 中是否包含 substr
strings.HasPrefix(s, prefix) 判断字符串 s 是否以 prefix 开头
strings.HasSuffix(s, suffix) 判断字符串 s 是否以 suffix 结尾
strings.Index(s, substr) 返回字符串 s 中 substr 第一次出现的位置, 如果没有出现则返回 -1
strings.LastIndex(s, substr) 返回字符串 s 中 substr 最后一次出现的位置, 如果没有出现则返回 -1
strings.Join(a[]string, sep) 将字符串切片 a 用 sep 连接起来
strings.Repeat(s, count) 重复 s count 次, 最后返回重复的字符串
strings.Map(func(rune) rune, s) 将字符串 s 中的每一个字符用 func 处理, 返回处理后的字符串
strings.Replace(s, old, new, n) 将字符串 s 中的前 n 个 old 替换为 new, n = -1 表示全部替换
strings.ToLower(s) 将字符串 s 中的字母转换为小写
strings.ToUpper(s) 将字符串 s 中的字母转换为大写
strings.TrimSpace(s) 去除字符串 s 前后的空格
strings.Trim(s, cutset) 去除字符串 s 前后的 cutset 中的字符
strings.TrimLeft(s, cutset) 去除字符串 s 前面的 cutset 中的字符
strings.TrimRight(s, cutset) 去除字符串 s 后面的 cutset 中的字符
strings.Fields(s) 将字符串 s 按照空格分割, 返回一个字符串切片
strings.NewReader(s) 将字符串 s 转换为一个 Reader

strconv 包
strconv.Itoa(i) 将整型 i 转换为字符串
strconv.Atoi(s) 将字符串 s 转换为整型
strconv.FormatFloat(f, fmt, prec, bitSize) 将浮点数 f 转换为字符串, fmt 表示格式, prec 表示精度, bitSize 表示 f 的来源类型(32: float32, 64: float64)
strconv.ParseFloat(s, bitSize) 将字符串 s 转换为浮点数, bitSize 表示结果的类型(32: float32, 64: float64)
strconv.FormatInt(i, base) 将整型 i 转换为字符串, base 表示进制(2, 8, 10, 16)
strconv.ParseInt(s, base, bitSize) 将字符串 s 转换为整型, base 表示进制(2, 8, 10, 16), bitSize 表示结果的类型(0: int, 8: int8, 16: int16, 32: int32, 64: int64)
strconv.FormatUint(i, base) 将无符号整型 i 转换为字符串, base 表示进制(2, 8, 10, 16)
strconv.ParseUint(s, base, bitSize) 将字符串 s 转换为无符号整型, base 表示进制(2, 8, 10, 16), bitSize 表示结果的类型(0: uint, 8: uint8, 16: uint16, 32: uint32, 64: uint64)
strconv.Quote(s) 将字符串 s 转换为带引号的字符串
strconv.QuoteRune(r) 将字符 r 转换为带引号的字符串
strconv.QuoteRuneToASCII(r) 将字符 r 转换为带引号的字符串, 并且使用 ASCII 编码
strconv.QuoteRuneToGraphic(r) 将字符 r 转换为带引号的字符串, 并且使用 Go 语言的转义格式
strconv.QuoteToASCII(s) 将字符串 s 转换为带引号的字符串, 并且使用 ASCII 编码
strconv.QuoteToGraphic(s) 将字符串 s 转换为带引号的字符串, 并且使用 Go 语言的转义格式
strconv.Unquote(s) 将带引号的字符串 s 转换为字符串
strconv.UnquoteChar(s, quote) 将带引号的字符串 s 转换为字符, quote 表示引号字符(单引号或双引号)
strconv.AppendQuote(dst, s) 将字符串 s 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面
strconv.AppendQuoteRune(dst, r) 将字符 r 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面
strconv.AppendQuoteRuneToASCII(dst, r) 将字符 r 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面, 并且使用 ASCII 编码
strconv.AppendQuoteRuneToGraphic(dst, r) 将字符 r 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面, 并且使用 Go 语言的转义格式
strconv.AppendQuoteToASCII(dst, s) 将字符串 s 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面, 并且使用 ASCII 编码
strconv.AppendQuoteToGraphic(dst, s) 将字符串 s 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面, 并且使用 Go 语言的转义格式
strconv.AppendUnquote(dst, s) 将带引号的字符串 s 转换为字符串, 并将结果追加到 dst 字节切片的后面
strconv.AppendUnquoteChar(dst, s, quote) 将带引号的字符串 s 转换为字符, 并将结果追加到 dst 字节切片的后面, quote 表示引号字符(单引号或双引号)
strconv.CanBackquote(s) 判断字符串 s 是否可以不带引号的方式输出
strconv.ErrRange 表示转换时值超出范围
strconv.ErrSyntax 表示语法错误
strconv.IntSize 表示 int 类型的位数
strconv.IsPrint(r) 判断字符 r 是否可以被 Go 语言的 %+q 格式化输出
strconv.NumError 表示转换错误
strconv.ErrRange 表示转换时值超出范围
strconv.ErrSyntax 表示语法错误
strconv.IntSize 表示 int 类型的位数
strconv.IsPrint(r) 判断字符 r 是否可以被 Go 语言的 %+q 格式化输出
strconv.NumError 表示转换错误
strconv.ErrRange 表示转换时值超出范围
strconv.ErrSyntax 表示语法错误
strconv.IntSize 表示 int 类型的位数
strconv.IsPrint(r) 判断字符 r 是否可以被 Go 语言的 %+q 格式化输出


string(b) 获得字节切片的字符串
string(r) 获得字符切片的字符串

strconv.Itoa(i) 将整型 i 转换为字符串
strconv.Atoi(s) 将字符串 s 转换为整型
strconv.ParseInt(s, base, bitSize) 将字符串 s 转换为整型, base 表示进制(2, 8, 10, 16), bitSize 表示结果的类型(0: int, 8: int8, 16: int16, 32: int32, 64: int64)
strconv.FormatInt(i, base) 将整型 i 转换为字符串, base 表示进制(2, 8, 10, 16)
strconv.ParseUint(s, base, bitSize) 将字符串 s 转换为无符号整型, base 表示进制(2, 8, 10, 16), bitSize 表示结果的类型(0: uint, 8: uint8, 16: uint16, 32: uint32, 64: uint64)
strconv.FormatUint(i, base) 将无符号整型 i 转换为字符串, base 表示进制(2, 8, 10, 16)
strconv.ParseFloat(s, bitSize) 将字符串 s 转换为浮点型, bitSize 表示结果的类型(32: float32, 64: float64)
strconv.FormatFloat(f, fmt, prec, bitSize) 将浮点型 f 转换为字符串, fmt 表示格式('b'(-ddddp±ddd, 二进制指数), 'e'(-d.dddde±dd, 十进制指数), 'E'(-d.ddddE±dd, 十进制指数), 'f'(-ddd.dddd, 无指数), 'g'(指数很大时用'e', 否则'f'), 'G'(指数很大时用'E', 否则'f')), prec 表示精度, bitSize 表示结果的类型(32: float32, 64: float64)
strconv.ParseBool(s) 将字符串 s 转换为布尔型
strconv.FormatBool(b) 将布尔型 b 转换为字符串
strconv.Quote(s) 将字符串 s 转换为带引号的字符串
strconv.QuoteToASCII(s) 将字符串 s 转换为带引号的字符串, 并且使用 ASCII 编码
strconv.QuoteToGraphic(s) 将字符串 s 转换为带引号的字符串, 并且使用 Go 语言的转义格式
strconv.Unquote(s) 将带引号的字符串 s 转换为字符串
strconv.UnquoteChar(s, quote) 将带引号的字符串 s 转换为字符, quote 表示引号字符(单引号或双引号)
strconv.AppendQuote(dst, s) 将字符串 s 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面
strconv.AppendQuoteRune(dst, r) 将字符 r 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面, 并且使用 Go 语言的转义格式
strconv.AppendQuoteRuneToASCII(dst, r) 将字符 r 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面, 并且使用 ASCII 编码
strconv.AppendQuoteRuneToGraphic(dst, r) 将字符 r 转换为带引号的字符串, 并将结果追加到 dst 字节切片的后面, 并且使用 Go 语言的转义格式
strconv.CanBackquote(s) 判断字符串 s 是否可以不带引号的方式输出
strconv.IsGraphic(r) 判断字符 r 是否可以被 Go 语言的 %+q 格式化输出
strconv.IsPrint(r) 判断字符 r 是否可以被 Go 语言的 %+q 格式化输出

## 面向对象

go 语言中的面向对象只支持封装, 不支持继承和多态, 而是使用接口来实现其它语言中继承和多态的功能

go 语言中没有 class, 只有 struct

## 4-1 结构体和方法

### 值接收者 vs 指针接收者

- 什么时候使用指针接收者

  - 要改变内容必须使用指针接收者
  - 结构过大也考虑使用指针接收者
  - 一致性: 如果有指针接收者, 最好都使用指针接收者

- 值接收者是 go 语言特有的
  - 指针接收者是大多数语言都有的. c++ this 指针, java this 是引用, python 的 self

- 值/指针接收 都可以接收 值/指针
  - 定义接收者时可以根据实际需要定义为 值接收者 或 指针接收者, 而使用时则可以使用 值类型变量或指针类型变量来调用方法


## 4-2 包和封装

### 封装

名字一般使用 CamelCase 命名??
首字母大写 public, 可以导出给其它包使用
首字母小写 private, 只能在本包中使用


### 包

每个目录中只能有一个包

包名可以和目录名不同

main 包包含一个可执行文件的入口, 入口文件中有一个 main 函数

为同一个结构体定义的方法必须放在同一个包内, 但可以放在不同的文件中

## 4-3 扩展已有类型

go 中没有继承的概念

如何扩充系统类型或别人的类型

- 定义别名: 最简单
- 使用组合: 最常用
- 内嵌: 可以省下很多代码

### 定义别名

### 使用组合

### 内嵌

## 5-1 依赖管理

github.com/uber-go/zap

### Go 语言的依赖管理

- 依赖的概念

- 依赖管理的三个阶段
  - GOPATH
  - GOVENDOR
  - Go mod

- 第三方依赖管理

## 5-2 GOPATH

GOPATH 是一个环境配置项. Linux 默认在 ~/go, Windows 默认在 %USERPROFILE%\go

所有的依赖都会放在 GOPATH 指向的目录中

带来的问题: 所有需要的第三方依赖和个人项目都要放在 GOPATH 目录中, GOPATH 目录会越来越大

windows 操作
```cmd

mkdir D:\data\temp\gopathtest\src

set GOPATH=D:\data\temp\gopathtest

rem 使用 goland 打开 D:\data\temp\gopathtest
rem 设置 Go > GOPATH, Project GOPATH: D:\data\temp\gopathtest
rem 取消勾选: "Use GOPATH that's defined in system environment"
rem 不使用 go mod, 在 Goland 中打开 Terminal
set GO111MODULE=off

rem 但是此命令在 windows 中不起作用
rem 在 goland terminal 中设置 go env -w GO111MODULE=off 会报错, 与系统环境变量冲突了, 但系统环境变量中并未找到 "GO111MODULE"
rem warning: go env -w GO111MODULE=... does not override conflicting OS environment variable

rem 需要在 cmd 中设置全局变量
go env -w GO111MODULE=off

rem 设置之后在系统环境变量中 GO111MODULE=off, 但在 goland terminal 中依然得到 GO111MODULE=auto

rem 可以在 Run/Debug Configurations 中设置 go build zaptest.go 运行的环境变量

rem "Environment": GO111MODULE=off

rem 下载 uber zap 包 https://github.com/uber-go/zap
go get -u go.uber.org/zap

```

- linux 下

```shell
mkdir /data/temp/gopathtest

查看 GOPATH 指向的目录

go env | findStr GOPATH
set GOPATH=C:\Users\zhiwei.wu\go

# 尽量不设置全局的 GOPATH
# 设置全局的 GOPATH
# go env -w GOPATH=/data/temp/gopathtest

# 设置当前命令窗口的 GOPATH
export GOPATH=/data/temp/gopathtest

# GOPATH 要求必须要有一个 src 的目录
mkdir /data/temp/gopathtest/src

```

新建项目 D:\data\temp\gopathtest\src\project1\zaptest.go

```go

package main

import "go.uber.org/zap"

func main() {
    log, _ := zap.NewProduction()
    log.Log(zap.InfoLevel, "hello zap")
    log.Warn("warn zap")

}

```

查看 `zap.NewProduction` 源码, 在 D:\data\temp\gopathtest\src\go.uber.org\zap\logger.go 中


修改 zaptest.go, 添加一个不存在的包, 查看报错时在哪里查找包

```go

package main

import "go.uber.org/zap"
import "github.com/aaa/bbb"

func main() {
    bbb.N()
    log, _ := zap.NewProduction()
    log.Log(zap.InfoLevel, "hello zap")
    log.Warn("warn zap")

}

```

运行报错

```

GOROOT=C:\ProgramFiles\Go #gosetup
GOPATH=D:\data\temp\gopathtest #gosetup
C:\ProgramFiles\Go\bin\go.exe build -o C:\Users\zhiwei.wu\AppData\Local\JetBrains\GoLand2023.1\tmp\GoLand\___1go_build_zaptest_go.exe D:\data\temp\gopathtest\src\zaptest.go #gosetup
zaptest.go:4:8: cannot find package "github.com/aaa/bbb" in any of:
    C:\ProgramFiles\Go\src\github.com\aaa\bbb (from $GOROOT)
    D:\data\temp\gopathtest\src\github.com\aaa\bbb (from $GOPATH)

Compilation finished with exit code 1

```

以上就是之前一定要在 D:\data\temp\gopathtest\ 建一个 src 目录的原因

如果有多个项目, 都依赖了 zap 库, 但依赖的 zap 库的版本不同, 就无法在 GOPATH 的方式下解决不同项目依赖同一库不同版本的问题

使用 vendor 方式来解决问题

在 project1 和 project2 中分别创建 vendor 目录, 把 go.uber.org 复制到 vendor 目录中, 再次运行以下代码

```go

package main

import "go.uber.org/zap"

import "github.com/aaa/bbb"

func main() {
    bbb.N()
    log, _ := zap.NewProduction()
    log.Log(zap.InfoLevel, "hello zap")
    log.Warn("warn zap")

}

```

```

GOROOT=C:\ProgramFiles\Go #gosetup
GOPATH=D:\data\temp\gopathtest #gosetup
C:\ProgramFiles\Go\bin\go.exe build -o C:\Users\zhiwei.wu\AppData\Local\JetBrains\GoLand2023.1\tmp\GoLand\___go_build_project1.exe project1 #gosetup
zaptest.go:5:8: cannot find package "github.com/aaa/bbb" in any of:
    D:\data\temp\gopathtest\src\project1\vendor\github.com\aaa\bbb (vendor tree)
    C:\ProgramFiles\Go\src\github.com\aaa\bbb (from $GOROOT)
    D:\data\temp\gopathtest\src\github.com\aaa\bbb (from $GOPATH)

Compilation finished with exit code 1

```

以上为报错信息, 可以看到, 会依照 vendor > GOROOT > GOPATH 的顺序去查找依赖, 这样就解决了 GOPATH 模式下不同项目依赖不同版本的问题

此时再查看项目中的 `zap.NewProduction`, 路径如下, 即 vendor 目录下
D:\data\temp\gopathtest\src\project1\vendor\go.uber.org\zap\logger.go

GOVENDOR

每个项目有自己的 vendor 目录, 存放第三方库

大量第三方依赖管理工具就是为了管理 vendor 目录而存在
  - glide, dep, go dep

https://github.com/Masterminds/glide

## 5-3 go mod 的使用

mkdir D:\David\Desktop\projects_study\imooc_class_180_go\code_study\learngo\ch05-dependency\class03-gomod\gomodtest

cd D:\David\Desktop\projects_study\imooc_class_180_go\code_study\learngo\ch05-dependency\class03-gomod\gomodtest

go mod init gomodtest

go get -u go.uber.org/zap
go: downloading go.uber.org/zap v1.24.0
go: downloading go.uber.org/atomic v1.7.0
go: downloading go.uber.org/multierr v1.6.0
go: downloading go.uber.org/multierr v1.11.0
go: downloading go.uber.org/atomic v1.11.0
go: added go.uber.org/atomic v1.11.0
go: added go.uber.org/multierr v1.11.0
go: added go.uber.org/zap v1.24.0


go.mod

```
module gomodtest

go 1.19

require (
    go.uber.org/atomic v1.11.0 // indirect
    go.uber.org/multierr v1.11.0 // indirect
    go.uber.org/zap v1.24.0 // indirect
)

```

go.sum

```

go.uber.org/atomic v1.11.0 h1:ZvwS0R+56ePWxUNi+Atn9dWONBPp/AUETXlHW0DxSjE=
go.uber.org/atomic v1.11.0/go.mod h1:LUxbIzbOniOlMKjJjyPfpl4v+PKK2cNJn91OQbhoJI0=
go.uber.org/multierr v1.11.0 h1:blXXJkSxSSfBVBlC76pxqeO+LN3aDfLQo+309xJstO0=
go.uber.org/multierr v1.11.0/go.mod h1:20+QtiLqy0Nd6FdQB9TLXag12DsQkrbs3htMFfDN80Y=
go.uber.org/zap v1.24.0 h1:FiJd5l1UOLj0wCgbSE0rwwXHzEdAZS6hiiSnxJN/D60=
go.uber.org/zap v1.24.0/go.mod h1:2kMP+WWQ8aoFoedH3T2sq6iJ2yDWpHbP0f6MQbS9Gkg=


```

新建 D:\David\Desktop\projects_study\imooc_class_180_go\code_study\learngo\ch05-dependency\class03-gomod\gomodtest\zaptest.go

```go

package main

import "go.uber.org/zap"

import "github.com/aaa/bbb"

func main() {
    bbb.N()
    log, _ := zap.NewProduction()
    log.Log(zap.InfoLevel, "hello zap")
    log.Warn("warn zap")

}

```

报错信息

```

GOROOT=C:\ProgramFiles\Go #gosetup
GOPATH=C:\Users\zhiwei.wu\go #gosetup
C:\ProgramFiles\Go\bin\go.exe build -o C:\Users\zhiwei.wu\AppData\Local\JetBrains\GoLand2023.1\tmp\GoLand\___go_build_gomodtest.exe gomodtest #gosetup
zaptest.go:5:8: no required module provides package github.com/aaa/bbb; to add it:
    go get github.com/aaa/bbb

Compilation finished with exit code 1

```

查看 zap.NewProduction 源码位置

C:\Users\zhiwei.wu\go\pkg\mod\go.uber.org\zap@v1.24.0\logger.go

go get -u golang.org/x/tools
go: downloading golang.org/x/tools v0.10.0
go: added golang.org/x/tools v0.10.0

go.mod

module gomodtest

go 1.19

require (
    go.uber.org/atomic v1.11.0 // indirect
    go.uber.org/multierr v1.11.0 // indirect
    go.uber.org/zap v1.24.0 // indirect
    golang.org/x/tools v0.10.0 // indirect
)


go.sum

go.uber.org/atomic v1.11.0 h1:ZvwS0R+56ePWxUNi+Atn9dWONBPp/AUETXlHW0DxSjE=
go.uber.org/atomic v1.11.0/go.mod h1:LUxbIzbOniOlMKjJjyPfpl4v+PKK2cNJn91OQbhoJI0=
go.uber.org/multierr v1.11.0 h1:blXXJkSxSSfBVBlC76pxqeO+LN3aDfLQo+309xJstO0=
go.uber.org/multierr v1.11.0/go.mod h1:20+QtiLqy0Nd6FdQB9TLXag12DsQkrbs3htMFfDN80Y=
go.uber.org/zap v1.24.0 h1:FiJd5l1UOLj0wCgbSE0rwwXHzEdAZS6hiiSnxJN/D60=
go.uber.org/zap v1.24.0/go.mod h1:2kMP+WWQ8aoFoedH3T2sq6iJ2yDWpHbP0f6MQbS9Gkg=
golang.org/x/tools v0.10.0 h1:tvDr/iQoUqNdohiYm0LmmKcBk+q86lb9EprIUFhHHGg=
golang.org/x/tools v0.10.0/go.mod h1:UJwyiVBsOA2uwvK/e5OY3GTpDUJriEd+/YlqAwLPmyM=

如何安装其它版本的依赖



go get -u go.uber.org/zap@v1.12
go: downloading go.uber.org/zap v1.12.0
go: downloading golang.org/x/lint v0.0.0-20190930215403-16217165b5de
go: downloading golang.org/x/lint v0.0.0-20210508222113-6edffad5e616
go: downgraded go.uber.org/zap v1.24.0 => v1.12.0
go: added golang.org/x/lint v0.0.0-20210508222113-6edffad5e616


go.mod

module gomodtest

go 1.19

require (
    go.uber.org/atomic v1.11.0 // indirect
    go.uber.org/multierr v1.11.0 // indirect
    go.uber.org/zap v1.12.0 // indirect
    golang.org/x/lint v0.0.0-20210508222113-6edffad5e616 // indirect
    golang.org/x/tools v0.10.0 // indirect
)


go.sum

github.com/BurntSushi/toml v0.3.1/go.mod h1:xHWCNGjB5oqiDr8zfno3MHue2Ht5sIBksp03qcyfWMU=
github.com/davecgh/go-spew v1.1.0/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
github.com/davecgh/go-spew v1.1.1/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
github.com/google/renameio v0.1.0/go.mod h1:KWCgfxg9yswjAJkECMjeO8J8rahYeXnNhOm40UhjYkI=
github.com/kisielk/gotool v1.0.0/go.mod h1:XhKaO+MFFWcvkIS/tQcRk01m1F5IRFswLeQ+oQHNcck=
github.com/kr/pretty v0.1.0/go.mod h1:dAy3ld7l9f0ibDNOQOHHMYYIIbhfbHSm3C4ZsoJORNo=
github.com/kr/pty v1.1.1/go.mod h1:pFQYn66WHrOpPYNljwOMqo10TkYh1fy3cYio2l3bCsQ=
github.com/kr/text v0.1.0/go.mod h1:4Jbv+DJW3UT/LiOwJeYQe1efqtUx/iVham/4vfdArNI=
github.com/pkg/errors v0.8.1/go.mod h1:bwawxfHBFNV+L2hUp1rHADufV3IMtnDRdf1r5NINEl0=
github.com/pmezard/go-difflib v1.0.0/go.mod h1:iKH77koFhYxTK1pcRnkKkqfTogsbg7gZNVY4sRDYZ/4=
github.com/rogpeppe/go-internal v1.3.0/go.mod h1:M8bDsm7K2OlrFYOpmOWEs/qY81heoFRclV5y23lUDJ4=
github.com/stretchr/objx v0.1.0/go.mod h1:HFkY916IF+rwdDfMAkV7OtwuqBVzrE8GR6GFx+wExME=
github.com/stretchr/testify v1.3.0/go.mod h1:M5WIy9Dh21IEIfnGCwXGc5bZfKNJtfHm1UVUgZn+9EI=
github.com/stretchr/testify v1.4.0/go.mod h1:j7eGeouHqKxXV5pUuKE4zz7dFj8WfuZ+81PSLYec5m4=
go.uber.org/atomic v1.5.0/go.mod h1:sABNBOSYdrvTF6hTgEIbc7YasKWGhgEQZyfxyTvoXHQ=
go.uber.org/atomic v1.11.0 h1:ZvwS0R+56ePWxUNi+Atn9dWONBPp/AUETXlHW0DxSjE=
go.uber.org/atomic v1.11.0/go.mod h1:LUxbIzbOniOlMKjJjyPfpl4v+PKK2cNJn91OQbhoJI0=
go.uber.org/multierr v1.3.0/go.mod h1:VgVr7evmIr6uPjLBxg28wmKNXyqE9akIJ5XnfpiKl+4=
go.uber.org/multierr v1.11.0 h1:blXXJkSxSSfBVBlC76pxqeO+LN3aDfLQo+309xJstO0=
go.uber.org/multierr v1.11.0/go.mod h1:20+QtiLqy0Nd6FdQB9TLXag12DsQkrbs3htMFfDN80Y=
go.uber.org/tools v0.0.0-20190618225709-2cfd321de3ee/go.mod h1:vJERXedbb3MVM5f9Ejo0C68/HhF8uaILCdgjnY+goOA=
go.uber.org/zap v1.12.0 h1:dySoUQPFBGj6xwjmBzageVL8jGi8uxc6bEmJQjA06bw=
go.uber.org/zap v1.12.0/go.mod h1:zwrFLgMcdUuIBviXEYEH1YKNaOBnKXsx2IPda5bBwHM=
go.uber.org/zap v1.24.0 h1:FiJd5l1UOLj0wCgbSE0rwwXHzEdAZS6hiiSnxJN/D60=
go.uber.org/zap v1.24.0/go.mod h1:2kMP+WWQ8aoFoedH3T2sq6iJ2yDWpHbP0f6MQbS9Gkg=
golang.org/x/crypto v0.0.0-20190308221718-c2843e01d9a2/go.mod h1:djNgcEr1/C05ACkg1iLfiJU5Ep61QUkGW8qpdssI0+w=
golang.org/x/crypto v0.0.0-20190510104115-cbcb75029529/go.mod h1:yigFU9vqHzYiE8UmvKecakEJjdnWj3jj499lnFckfCI=
golang.org/x/crypto v0.0.0-20191011191535-87dc89f01550/go.mod h1:yigFU9vqHzYiE8UmvKecakEJjdnWj3jj499lnFckfCI=
golang.org/x/lint v0.0.0-20190930215403-16217165b5de/go.mod h1:6SW0HCj/g11FgYtHlgUYUwCkIfeOF89ocIRzGO/8vkc=
golang.org/x/lint v0.0.0-20210508222113-6edffad5e616 h1:VLliZ0d+/avPrXXH+OakdXhpJuEoBZuwh1m2j7U6Iug=
golang.org/x/lint v0.0.0-20210508222113-6edffad5e616/go.mod h1:3xt1FjdF8hUf6vQPIChWIBhFzV8gjjsPE/fR3IyQdNY=
golang.org/x/mod v0.0.0-20190513183733-4bf6d317e70e/go.mod h1:mXi4GBBbnImb6dmsKGUJ2LatrhH/nqhxcFungHvyanc=
golang.org/x/mod v0.1.1-0.20191105210325-c90efee705ee/go.mod h1:QqPTAvyqsEbceGzBzNggFXnrqF1CaUcvgkdR5Ot7KZg=
golang.org/x/net v0.0.0-20190311183353-d8887717615a/go.mod h1:t9HGtf8HONx5eT2rtn7q6eTqICYqUVnKs3thJo3Qplg=
golang.org/x/net v0.0.0-20190404232315-eb5bcb51f2a3/go.mod h1:t9HGtf8HONx5eT2rtn7q6eTqICYqUVnKs3thJo3Qplg=
golang.org/x/net v0.0.0-20190620200207-3b0461eec859/go.mod h1:z5CRVTTTmAJ677TzLLGU+0bjPO0LkuOLi4/5GtJWs/s=
golang.org/x/sync v0.0.0-20190423024810-112230192c58/go.mod h1:RxMgew5VJxzue5/jJTE5uejpjVlOe/izrB70Jof72aM=
golang.org/x/sys v0.0.0-20190215142949-d0b11bdaac8a/go.mod h1:STP8DvDyc/dI5b8T5hshtkjS+E42TnysNCUPdjciGhY=
golang.org/x/sys v0.0.0-20190412213103-97732733099d/go.mod h1:h1NjWce9XRLGQEsW7wpKNCjG9DtNlClVuFLEZdDNbEs=
golang.org/x/text v0.3.0/go.mod h1:NqM8EUOU14njkJ3fqMW+pc6Ldnwhi/IjpwHt7yyuwOQ=
golang.org/x/tools v0.0.0-20190311212946-11955173bddd/go.mod h1:LCzVGOaR6xXOjkQ3onu1FJEFr0SW1gC7cKk1uF8kGRs=
golang.org/x/tools v0.0.0-20190621195816-6e04913cbbac/go.mod h1:/rFqwRUd4F7ZHNgwSSTFct+R/Kf4OFW1sUzUTQQTgfc=
golang.org/x/tools v0.0.0-20191029041327-9cc4af7d6b2c/go.mod h1:b+2E5dAYhXwXZwtnZ6UAqBI28+e2cm9otk0dWdXHAEo=
golang.org/x/tools v0.0.0-20191029190741-b9c20aec41a5/go.mod h1:b+2E5dAYhXwXZwtnZ6UAqBI28+e2cm9otk0dWdXHAEo=
golang.org/x/tools v0.0.0-20200130002326-2f3ba24bd6e7/go.mod h1:TB2adYChydJhpapKDTa4BR/hXlZSLoq2Wpct/0txZ28=
golang.org/x/tools v0.10.0 h1:tvDr/iQoUqNdohiYm0LmmKcBk+q86lb9EprIUFhHHGg=
golang.org/x/tools v0.10.0/go.mod h1:UJwyiVBsOA2uwvK/e5OY3GTpDUJriEd+/YlqAwLPmyM=
golang.org/x/xerrors v0.0.0-20190717185122-a985d3407aa7/go.mod h1:I/5z698sn9Ka8TeJc9MKroUUfqBBauWjQqLJ2OPfmY0=
golang.org/x/xerrors v0.0.0-20191011141410-1b5146add898/go.mod h1:I/5z698sn9Ka8TeJc9MKroUUfqBBauWjQqLJ2OPfmY0=
gopkg.in/check.v1 v0.0.0-20161208181325-20d25e280405/go.mod h1:Co6ibVJAznAaIkqp8huTwlJQCZ016jof/cbN4VW5Yz0=
gopkg.in/check.v1 v1.0.0-20180628173108-788fd7840127/go.mod h1:Co6ibVJAznAaIkqp8huTwlJQCZ016jof/cbN4VW5Yz0=
gopkg.in/errgo.v2 v2.1.0/go.mod h1:hNsd1EY+bozCKY1Ytp96fpM3vjJbqLJn88ws8XvfDNI=
gopkg.in/yaml.v2 v2.2.2/go.mod h1:hI93XBmqTisBFMUTm0b8Fm+jr3Dg1NNxqwp+5A1VGuI=
honnef.co/go/tools v0.0.1-2019.2.3/go.mod h1:a3bituU0lyd329TUQxRnasdCoJDkEUEAqEt0JzvZhAg=

go.sum 中 1.12 和 1.24 同时存在, 可以使用 `go mod tidy` 命令来删除不使用的依赖

go mod tidy
go: downloading github.com/pkg/errors v0.8.1
go: downloading github.com/stretchr/testify v1.7.0
go: downloading golang.org/x/sync v0.3.0
go: downloading golang.org/x/sys v0.9.0
go: downloading golang.org/x/mod v0.11.0


go.mod

module gomodtest

go 1.19

require go.uber.org/zap v1.12.0

require (
    go.uber.org/atomic v1.11.0 // indirect
    go.uber.org/multierr v1.11.0 // indirect
    golang.org/x/lint v0.0.0-20210508222113-6edffad5e616 // indirect
    golang.org/x/tools v0.10.0 // indirect
)


go.sum

github.com/BurntSushi/toml v0.3.1/go.mod h1:xHWCNGjB5oqiDr8zfno3MHue2Ht5sIBksp03qcyfWMU=
github.com/davecgh/go-spew v1.1.0/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
github.com/davecgh/go-spew v1.1.1 h1:vj9j/u1bqnvCEfJOwUhtlOARqs3+rkHYY13jYWTU97c=
github.com/davecgh/go-spew v1.1.1/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
github.com/google/renameio v0.1.0/go.mod h1:KWCgfxg9yswjAJkECMjeO8J8rahYeXnNhOm40UhjYkI=
github.com/kisielk/gotool v1.0.0/go.mod h1:XhKaO+MFFWcvkIS/tQcRk01m1F5IRFswLeQ+oQHNcck=
github.com/kr/pretty v0.1.0/go.mod h1:dAy3ld7l9f0ibDNOQOHHMYYIIbhfbHSm3C4ZsoJORNo=
github.com/kr/pty v1.1.1/go.mod h1:pFQYn66WHrOpPYNljwOMqo10TkYh1fy3cYio2l3bCsQ=
github.com/kr/text v0.1.0/go.mod h1:4Jbv+DJW3UT/LiOwJeYQe1efqtUx/iVham/4vfdArNI=
github.com/pkg/errors v0.8.1 h1:iURUrRGxPUNPdy5/HRSm+Yj6okJ6UtLINN0Q9M4+h3I=
github.com/pkg/errors v0.8.1/go.mod h1:bwawxfHBFNV+L2hUp1rHADufV3IMtnDRdf1r5NINEl0=
github.com/pmezard/go-difflib v1.0.0 h1:4DBwDE0NGyQoBHbLQYPwSUPoCMWR5BEzIk/f1lZbAQM=
github.com/pmezard/go-difflib v1.0.0/go.mod h1:iKH77koFhYxTK1pcRnkKkqfTogsbg7gZNVY4sRDYZ/4=
github.com/rogpeppe/go-internal v1.3.0/go.mod h1:M8bDsm7K2OlrFYOpmOWEs/qY81heoFRclV5y23lUDJ4=
github.com/stretchr/objx v0.1.0/go.mod h1:HFkY916IF+rwdDfMAkV7OtwuqBVzrE8GR6GFx+wExME=
github.com/stretchr/testify v1.3.0/go.mod h1:M5WIy9Dh21IEIfnGCwXGc5bZfKNJtfHm1UVUgZn+9EI=
github.com/stretchr/testify v1.4.0/go.mod h1:j7eGeouHqKxXV5pUuKE4zz7dFj8WfuZ+81PSLYec5m4=
github.com/stretchr/testify v1.7.0 h1:nwc3DEeHmmLAfoZucVR881uASk0Mfjw8xYJ99tb5CcY=
go.uber.org/atomic v1.5.0/go.mod h1:sABNBOSYdrvTF6hTgEIbc7YasKWGhgEQZyfxyTvoXHQ=
go.uber.org/atomic v1.11.0 h1:ZvwS0R+56ePWxUNi+Atn9dWONBPp/AUETXlHW0DxSjE=
go.uber.org/atomic v1.11.0/go.mod h1:LUxbIzbOniOlMKjJjyPfpl4v+PKK2cNJn91OQbhoJI0=
go.uber.org/multierr v1.3.0/go.mod h1:VgVr7evmIr6uPjLBxg28wmKNXyqE9akIJ5XnfpiKl+4=
go.uber.org/multierr v1.11.0 h1:blXXJkSxSSfBVBlC76pxqeO+LN3aDfLQo+309xJstO0=
go.uber.org/multierr v1.11.0/go.mod h1:20+QtiLqy0Nd6FdQB9TLXag12DsQkrbs3htMFfDN80Y=
go.uber.org/tools v0.0.0-20190618225709-2cfd321de3ee/go.mod h1:vJERXedbb3MVM5f9Ejo0C68/HhF8uaILCdgjnY+goOA=
go.uber.org/zap v1.12.0 h1:dySoUQPFBGj6xwjmBzageVL8jGi8uxc6bEmJQjA06bw=
go.uber.org/zap v1.12.0/go.mod h1:zwrFLgMcdUuIBviXEYEH1YKNaOBnKXsx2IPda5bBwHM=
golang.org/x/crypto v0.0.0-20190308221718-c2843e01d9a2/go.mod h1:djNgcEr1/C05ACkg1iLfiJU5Ep61QUkGW8qpdssI0+w=
golang.org/x/crypto v0.0.0-20190510104115-cbcb75029529/go.mod h1:yigFU9vqHzYiE8UmvKecakEJjdnWj3jj499lnFckfCI=
golang.org/x/crypto v0.0.0-20191011191535-87dc89f01550/go.mod h1:yigFU9vqHzYiE8UmvKecakEJjdnWj3jj499lnFckfCI=
golang.org/x/lint v0.0.0-20190930215403-16217165b5de/go.mod h1:6SW0HCj/g11FgYtHlgUYUwCkIfeOF89ocIRzGO/8vkc=
golang.org/x/lint v0.0.0-20210508222113-6edffad5e616 h1:VLliZ0d+/avPrXXH+OakdXhpJuEoBZuwh1m2j7U6Iug=
golang.org/x/lint v0.0.0-20210508222113-6edffad5e616/go.mod h1:3xt1FjdF8hUf6vQPIChWIBhFzV8gjjsPE/fR3IyQdNY=
golang.org/x/mod v0.0.0-20190513183733-4bf6d317e70e/go.mod h1:mXi4GBBbnImb6dmsKGUJ2LatrhH/nqhxcFungHvyanc=
golang.org/x/mod v0.1.1-0.20191105210325-c90efee705ee/go.mod h1:QqPTAvyqsEbceGzBzNggFXnrqF1CaUcvgkdR5Ot7KZg=
golang.org/x/mod v0.11.0 h1:bUO06HqtnRcc/7l71XBe4WcqTZ+3AH1J59zWDDwLKgU=
golang.org/x/net v0.0.0-20190311183353-d8887717615a/go.mod h1:t9HGtf8HONx5eT2rtn7q6eTqICYqUVnKs3thJo3Qplg=
golang.org/x/net v0.0.0-20190404232315-eb5bcb51f2a3/go.mod h1:t9HGtf8HONx5eT2rtn7q6eTqICYqUVnKs3thJo3Qplg=
golang.org/x/net v0.0.0-20190620200207-3b0461eec859/go.mod h1:z5CRVTTTmAJ677TzLLGU+0bjPO0LkuOLi4/5GtJWs/s=
golang.org/x/sync v0.0.0-20190423024810-112230192c58/go.mod h1:RxMgew5VJxzue5/jJTE5uejpjVlOe/izrB70Jof72aM=
golang.org/x/sync v0.3.0 h1:ftCYgMx6zT/asHUrPw8BLLscYtGznsLAnjq5RH9P66E=
golang.org/x/sys v0.0.0-20190215142949-d0b11bdaac8a/go.mod h1:STP8DvDyc/dI5b8T5hshtkjS+E42TnysNCUPdjciGhY=
golang.org/x/sys v0.0.0-20190412213103-97732733099d/go.mod h1:h1NjWce9XRLGQEsW7wpKNCjG9DtNlClVuFLEZdDNbEs=
golang.org/x/sys v0.9.0 h1:KS/R3tvhPqvJvwcKfnBHJwwthS11LRhmM5D59eEXa0s=
golang.org/x/text v0.3.0/go.mod h1:NqM8EUOU14njkJ3fqMW+pc6Ldnwhi/IjpwHt7yyuwOQ=
golang.org/x/tools v0.0.0-20190311212946-11955173bddd/go.mod h1:LCzVGOaR6xXOjkQ3onu1FJEFr0SW1gC7cKk1uF8kGRs=
golang.org/x/tools v0.0.0-20190621195816-6e04913cbbac/go.mod h1:/rFqwRUd4F7ZHNgwSSTFct+R/Kf4OFW1sUzUTQQTgfc=
golang.org/x/tools v0.0.0-20191029041327-9cc4af7d6b2c/go.mod h1:b+2E5dAYhXwXZwtnZ6UAqBI28+e2cm9otk0dWdXHAEo=
golang.org/x/tools v0.0.0-20191029190741-b9c20aec41a5/go.mod h1:b+2E5dAYhXwXZwtnZ6UAqBI28+e2cm9otk0dWdXHAEo=
golang.org/x/tools v0.0.0-20200130002326-2f3ba24bd6e7/go.mod h1:TB2adYChydJhpapKDTa4BR/hXlZSLoq2Wpct/0txZ28=
golang.org/x/tools v0.10.0 h1:tvDr/iQoUqNdohiYm0LmmKcBk+q86lb9EprIUFhHHGg=
golang.org/x/tools v0.10.0/go.mod h1:UJwyiVBsOA2uwvK/e5OY3GTpDUJriEd+/YlqAwLPmyM=
golang.org/x/xerrors v0.0.0-20190717185122-a985d3407aa7/go.mod h1:I/5z698sn9Ka8TeJc9MKroUUfqBBauWjQqLJ2OPfmY0=
golang.org/x/xerrors v0.0.0-20191011141410-1b5146add898/go.mod h1:I/5z698sn9Ka8TeJc9MKroUUfqBBauWjQqLJ2OPfmY0=
gopkg.in/check.v1 v0.0.0-20161208181325-20d25e280405/go.mod h1:Co6ibVJAznAaIkqp8huTwlJQCZ016jof/cbN4VW5Yz0=
gopkg.in/check.v1 v1.0.0-20180628173108-788fd7840127/go.mod h1:Co6ibVJAznAaIkqp8huTwlJQCZ016jof/cbN4VW5Yz0=
gopkg.in/errgo.v2 v2.1.0/go.mod h1:hNsd1EY+bozCKY1Ytp96fpM3vjJbqLJn88ws8XvfDNI=
gopkg.in/yaml.v2 v2.2.2/go.mod h1:hI93XBmqTisBFMUTm0b8Fm+jr3Dg1NNxqwp+5A1VGuI=
gopkg.in/yaml.v3 v3.0.1 h1:fxVm/GzAzEWqLHuvctI91KS9hhNmmWOoWu0XTYJS7CA=
honnef.co/go/tools v0.0.1-2019.2.3/go.mod h1:a3bituU0lyd329TUQxRnasdCoJDkEUEAqEt0JzvZhAg=

```go

package main

import "go.uber.org/zap"

func main() {
    log, _ := zap.NewProduction()
    log.Warn("warn zap")

}

```

查看 zap.NewProduction 源码

C:\Users\zhiwei.wu\go\pkg\mod\go.uber.org\zap@v1.12.0\logger.go

#### 如何添加依赖

方法1, 直接使用 go get 命令

go get github.com/gin-gonic/gin

go.mod

module gomodtest

go 1.19

require (
    github.com/gin-gonic/gin v1.9.1
    go.uber.org/zap v1.12.0
)

require (
    github.com/bytedance/sonic v1.9.1 // indirect
    github.com/chenzhuoyu/base64x v0.0.0-20221115062448-fe3a3abad311 // indirect
    github.com/gabriel-vasile/mimetype v1.4.2 // indirect
    github.com/gin-contrib/sse v0.1.0 // indirect
    github.com/go-playground/locales v0.14.1 // indirect
    github.com/go-playground/universal-translator v0.18.1 // indirect
    github.com/go-playground/validator/v10 v10.14.0 // indirect
    github.com/goccy/go-json v0.10.2 // indirect
    github.com/json-iterator/go v1.1.12 // indirect
    github.com/klauspost/cpuid/v2 v2.2.4 // indirect
    github.com/leodido/go-urn v1.2.4 // indirect
    github.com/mattn/go-isatty v0.0.19 // indirect
    github.com/modern-go/concurrent v0.0.0-20180306012644-bacd9c7ef1dd // indirect
    github.com/modern-go/reflect2 v1.0.2 // indirect
    github.com/pelletier/go-toml/v2 v2.0.8 // indirect
    github.com/twitchyliquid64/golang-asm v0.15.1 // indirect
    github.com/ugorji/go/codec v1.2.11 // indirect
    go.uber.org/atomic v1.11.0 // indirect
    go.uber.org/multierr v1.11.0 // indirect
    golang.org/x/arch v0.3.0 // indirect
    golang.org/x/crypto v0.9.0 // indirect
    golang.org/x/lint v0.0.0-20190930215403-16217165b5de // indirect
    golang.org/x/net v0.10.0 // indirect
    golang.org/x/sys v0.8.0 // indirect
    golang.org/x/text v0.9.0 // indirect
    golang.org/x/tools v0.6.0 // indirect
    google.golang.org/protobuf v1.30.0 // indirect
    gopkg.in/yaml.v3 v3.0.1 // indirect
)


方法2, 直接在代码中引入依赖, 运行时会自动下载依赖并更新 go.mod 文件 ?? 不成功

```go

package main

import "go.uber.org/zap"
import _ "github.com/gin-gonic/gin"

func main() {
    log, _ := zap.NewProduction()
    log.Warn("warn zap")
}

```

但运行一个 go 文件会把此文件所需要的依赖添加到 go.mod 中, 如何把所有 go 文件所需要的依赖都添加到 go.mod 中??

使用以下命令编译本目录及其所有子目录下的 go 文件, 同时下载依赖并把依赖添加到 go.mod 中
go build ./...

如果项目之前是以 vendor, glide 等方式管理依赖的, 使用 go mod init xxx 会自动识别 vendor, glide 等的信息, 自动生成 go.mod 文件

- go mod 使用总结

  - 由 go 命令统一进行管理, 用户不必关心目录结构
  - 初始化 go mod init xxx
  - 增加依赖 go get
  - 更新依赖 go get xxx[@v...]
  - 更新依赖 go build ./... 不产生任何可执行文件, 只是检查编译是否通过
  - 删除不使用的依赖 go mod tidy
  - 将旧项目迁移到 go mod: `go mod init xxx`, `go build ./...`



Go mod下本地目录结构
Go mod下如何引用本项目的包呢? 
大家只要记住这个公式即可: 

import的路径 = go.mod下的module name + 包相对于go.mod的相对目录

举例: 
我们的课程示例代码中: https://git.imooc.com/coding-180/coding-180/src/master/go.mod

module imooc.com/ccmouse/learngo
tree包在: https://git.imooc.com/coding-180/coding-180/src/master/lang/tree

相对于go.mod的相对路径为: lang/tree

所以引用tree包时: 

import "imooc.com/ccmouse/learngo/lang/tree"
在项目内的任一地方, 都是用这个路径进行import. 
例: 

https://git.imooc.com/coding-180/coding-180/src/master/lang/tree/treeentry/entry.go
https://git.imooc.com/coding-180/coding-180/src/master/lang/tree/treeentry_embedded/entry.go

## 5-4 目录的整理

每一个含有 main 函数的文件都必须要在自己单独的目录中, 不能把含有 main 函数的多个文件放在同一个目录中

查看 go 官方包的文件组织结构

go get golang.org/x/tools

查看源文件

C:\Users\zhiwei.wu\go\pkg\mod\golang.org\x\tools@v0.10.0

go build ./... 只检查编译是否通过

go install ./... 编译所有 go 文件, 存放在 GOPATH 目录下

go env GOPATH

C:\Users\zhiwei.wu\go


## 6-1 接口的概念

大的工程中如何模块化, 可配置, 如何并发. interface.

强类型语言: 熟悉接口的概念
弱类型语言: 没有或很少有接口的概念

理解了接口, 才能做到在模块的设计时, 理解接口之间的抽象, 完成模块与模块之间的交互

接口的详解: "使用 Google Guice 实现依赖注入"
  - 程序之中模块与模块之间的依赖

段子
  - 小孩子才分对错
  - 大人只看利弊


## 6-2 duck typing 的概念

go 是面向接口的编程语言, 和传统意义上的面向对象不同, 没有复杂的继承, 多态, 只有封装. 而其它语言中继承和多态实现的功能, 则是由接口来完成的


大黄鸭是鸭子吗?
传统生物学意义上来看, 它不是鸭子
从 duck typing 的角度来看, 长得像鸭子, 叫声像鸭子, 走路像鸭子, 它就是鸭子

duck typing 描述事件的外部行为而非内部结构

duck typing 要求动态绑定, 但 go 语言中无此要求

不同的人可能会有不同的观点, 吃货可能认为能吃的才是鸭子, 玩具大黄鸭不是鸭子, 而小朋友可能认为是鸭子. 所以要从使用者的角度来判断. 

go 中的接口由使用者来定义, 只要实现了接口中定义的方法, 都可以拿来当成接口的一种实现, 类似于 duck typing 的概念

python 中的 duck typing

```python

def download(downloader):
    return downloader.get("http://www.httpbin.org/get")

```

不管传入的 downloader 是什么类型的, 只要其中有 get 方法, 就可以正常使用
python 中只有运行时才知道传入的 downloader 有没有 get 方法
通常需要注释来说明接口

C++ 中的 duck typing

使用模板来支持

```c++

template <class R>
string download(const R& downloader) {
    return downloader.get("http://www.httpbin.org/get")
}

```

编译时才知道传入的 downloader 有没有 get 方法

还是需要注释来说明接口如何使用


java 中的类似代码

```java

<D extends Downloader>

String download(D d) {
    return d.get("http://www.htttbin.org/get")
}

```

传入的参数 d 必须要显式的实现 Downloader 接口, 必须要实现 get 方法
但不是 dock typing
优点是不需要使用注释来提醒实现者, 但是无法实现多个接口
如一个 download 函数, 需要的参数要同时实现 Readable 和 Appendable, Java 中很难实现. 
apache polygene 实现了起来

go 语言中的 duck typing

- 同时具有 python, C++ duck typing 的灵活性
- 又具有 java 的类型检查, 而不需要通过注释来说明需要的参数类型


## 6-3 接口的定义和实现

接口的定义

使用者   ---->   实现者
download        downloader

download 接收一个 downloader, 调用 downloader 的 get 方法, 获取结果并返回

接口由使用者定义, 而传统的面向对象中, 接口都是由实现者来定义的
更类似于 "由使用者来确定大黄鸭是不是鸭子" 的 dock typing 的概念

接口确定了要实现的方法, 实现者不需要显式实现某个接口, 只需要实现接口的方法即可

## 6-4 接口的值类型

接口变量里面有什么

在其它编程语言中, 接口变量可能只是一个引用或指针, 指向真实的实现者

但在 go 语言中, 所有的类型都是值类型, 所以需要搞清楚接口变量中包含着哪些内容

- 接口变量自带指针
  - 接口变量同样采用值传递, 几乎不会使用到接口的指针
  - 指针接收者只能以指针方式使用, 值接收者值方式和指针方式都可以

查看接口变量

  - 表示任何类型: interface {}
  - Type Assertion
  - Type Switch


### 6-6 常用系统接口


C:/ProgramFiles/Go/src/fmt/print.go:62

```go

type Stringer interface {
    String() string
}

```

相当于其它语言中的 toString 






io.ReadWriteCloser
io.ReadWriter
io.ReadWriteSeeker


C:/ProgramFiles/Go/src/io/io.go:96

```go

type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

type Closer interface {
    Close() error
}

type Seeker interface {
    Seek(offset int64, whence int) (int64, error)
}

type ReadWriter interface {
    Reader
    Writer
}

type ReadCloser interface {
    Reader
    Closer
}

type WriteCloser interface {
    Writer
    Closer
}

type ReadWriteCloser interface {
    Reader
    Writer
    Closer
}

```

Reader Writer 的对象 p 可以是文件, 网络 io, bytes, slice, string 等, 从 p 中读取数据, 或者把数据写入到 p 中

os.Open(filename) 返回一个 File 对象, File 对象实现了很多方法, 有 Reader, Writer, 


```go

func Fprintf(w io.Writer, format string, a ...any) (n int, err error) {
    p := newPrinter()
    p.doPrintf(format, a)
    n, err = w.Write(p.buf)
    p.free()
    return
}

func Fscanf(r io.Reader, format string, a ...any) (n int, err error) {
    s, old := newScanState(r, false, false)
    n, err = s.doScanf(format, a)
    s.free(old)
    return
}

```

很多底层函数的参数都是 Reader 或 Writer, 所以在使用的过程中, 把函数参数定义为 Reader 和 Writer, 就能很方便的调用系统的方法


## 7-1 函数式编程

函数与装饰

```go

func adder() func(value int) int {
    sum := 0
    return func(value int) int {
        sum += value
        return sum
    }
}

func main() {
    adder := adder()
    for i := 0; i < 10; i++ {
        fmt.Println(adder(i))
    }
}

```

### 函数式编程 vs 函数指针

- 函数是一等公民: 参数, 变量, 返回值都可以是函数
- 高阶函数
- 函数 > 闭包

```go

package main

import "fmt"

func add() func(int) int {
    sum := 0
    return func(v int) int {
        sum += v
        return sum
    }
}

func main() {
    a := add()
    for i := 0; i < 10; i++ {
        // 0 1 3 6 10 15 21 28 36 45 55
        fmt.Printf("0 + 1 + ... + %d = %d\n", i, a(i))
    }
}

```

python 中的闭包

python 原生支持闭包

可以使用 `f.__closure__` 来查看闭包的内容

```python

def adder():
    sum = 0

    def f(value):
        # 需要声明使用的不是局部变量
        nonlocal sum
        sum += value
        return sum

    return f

```





## 7-2 函数式编程例一



### 有关这里Read函数被调用的时机问题

许多同学都有疑问, 我们在这里写了一个Read函数, 它是如何被调用的呢? Read函数比较底层, 我们一般不会直接调用, 它是通过下面的Scanner间接调用的. 我们先从接口的角度看, 再剖析源码. 

首先从接口的角度. 从接口的角度来看, 我们不需要关心Read函数什么时候被调用. 
我们看到printFileContents函数需要接受一个io.Reader接口的变量, 名叫reader, 并且把这个reader变量传给了NewScanner. 什么样的类型才能满足io.Reader接口的要求呢? 就是实现了Read函数的类型. 我们只要正确实现了Read函数, 程序就可以正确的运行. 我们看到NewScanner的时候把reader变量交给它, NewScanner函数里面, 可能就会在这时候调用Read, 它返回了scanner, 这个scanner我们又做了scanner.Scan()和scanner.Text(), 所以这两处它也有可能调用Read. （注意我这里都是说**有可能**, 具体到底哪里不知道也不需要知道）. 这就是接口的意义, 我根据你的io.Reader的规范实现了Read函数, 就能够放到任意需要接受io.Reader参数的地方去用. 对方调用Read函数的方式和时机可能非常复杂, 我们不用关心. 
那不知道的话, 怎么调试呢? 我们还是看自己实现的这个Read函数的参数和返回值对不对就可以, 看他到底有没有符合io.Reader接口给定的要求. 而不是看Scanner到底在什么时候调用我们的Read函数. 

好, 那么我们接下来再源码剖析, 看看到底什么时候调用的, 虽然不需要知道, 但我们出于学习的目的, 想要知道. 
Scanner的源码在这里: 

第85行: 

```
// NewScanner returns a new Scanner to read from r.
// The split function defaults to ScanLines.
func NewScanner(r io.Reader) *Scanner {
	return &Scanner{
		r:            r,
		split:        ScanLines,
		maxTokenSize: MaxScanTokenSize,
	}
}
```

这里把我们给他的reader存起来, 叫做r. 

第126行, 一个巨大的Scan函数, 做的事情是把读取的内容放到s.token里面, 直到遇到下一个分隔符. 默认情况下, 下一个分隔符就是换行, 所以它会一行一行读. 这里非常复杂, 需要处理各种缓冲区的情况, 有兴趣的同学可以细看. 我们关心的是210行: 

```
		// Finally we can read some input. Make sure we don't get stuck with
		// a misbehaving Reader. Officially we don't need to do this, but let's
		// be extra careful: Scanner is for safe, simple jobs.
		for loop := 0; ; {
			n, err := s.r.Read(s.buf[s.end:len(s.buf)])
			...
```

正像它注释里所说的, 终于, 我们可以调用Read来读取数据了. 前面所有的那将近100行, 都在处理各种情况以及错误, 或者我们可能之前的Read已经读了部分数据到缓冲区, 那先要处理它们. 

最后, 第112行

```
func (s *Scanner) Text() string {
	return string(s.token)
}
```

这里Text函数比较简单, 只是把Scan函数读到的token转换成string返回出来. 

正像我所说的, 这个io.Reader是个比较底层的接口, 它要正确的进行调用非常之困难, 通常我们不会直接**调用**Read函数, 而是通过其他库函数的帮助. 不过正因为调用起来很困难, Read函数实现起来并不太困难. 它作为一个底层的函数, 规定了非常直接的读取数据的能力而已. 
使用io.Reader接口的地方非常多, 比如

- ioutil.ReadAll
- http.Post
- json.Decoder.Decode
- 等等



### 有关这个p太小的问题及其解法

这里我们说如果p太小会有问题. 具体体现在这里: 

Go playground链接: https://play.studygolang.com/p/5PalFmJyc6E

```
package main

import (
	"fmt"
	"strings"
)

// 1, 1, 2, 3, 5, 8, 13, 21, 34, ...
func Fibonacci() func() int {
	a, b := 0, 1
	return func() int {
		a, b = b, a+b
		return a
	}
}

type intGen func() int

func (g intGen) Read(p []byte) (n int, err error) {
	next := g()
	s := fmt.Sprintf("%d\n", next)

	// TODO: incorrect if p is too small!
	return strings.NewReader(s).Read(p)
}

func main() {
	var f intGen = Fibonacci()
	for i := 0; i < 20; i++ {
		b := make([]byte, 1)
		n, err := f.Read(b)
		fmt.Printf("%d bytes read: %q. err = %v\n", n, b, err)
	}
}
```

```
1 bytes read: "1". err = <nil>
1 bytes read: "1". err = <nil>
1 bytes read: "2". err = <nil>
1 bytes read: "3". err = <nil>
1 bytes read: "5". err = <nil>
1 bytes read: "8". err = <nil>
1 bytes read: "1". err = <nil>
1 bytes read: "2". err = <nil>
1 bytes read: "3". err = <nil>
1 bytes read: "5". err = <nil>
1 bytes read: "8". err = <nil>
1 bytes read: "1". err = <nil>
1 bytes read: "2". err = <nil>
1 bytes read: "3". err = <nil>
1 bytes read: "6". err = <nil>
1 bytes read: "9". err = <nil>
1 bytes read: "1". err = <nil>
1 bytes read: "2". err = <nil>
1 bytes read: "4". err = <nil>
1 bytes read: "6". err = <nil>
```



我们可以看到, 每个数它只读了开头第一位, 这是不对的. 

修改方法参考如下, 正像视频里说的, 把中间结果缓存起来, 这里就要把intGen做成一个struct才行了: 
链接: https://play.studygolang.com/p/P42Kr3qhSg1

```go
package main

import (
	"fmt"
	"io"
	"strings"
)

// 1, 1, 2, 3, 5, 8, 13, 21, 34, ...
func Fibonacci() func() int {
	a, b := 0, 1
	return func() int {
		a, b = b, a+b
		return a
	}
}

type intGen struct {
	gen           func() int
	currentReader io.Reader
}

func (g *intGen) Read(p []byte) (n int, err error) {
	err = io.EOF
	if g.currentReader != nil {
		n, err = g.currentReader.Read(p)
	}
	if err == io.EOF {
		next := g.gen()
		s := fmt.Sprintf("%d\n", next)
		g.currentReader = strings.NewReader(s)
		if n == 0 {
			n, err = g.currentReader.Read(p)
		}
	}
	return n, err
}

func main() {
	f := &intGen{
		gen: Fibonacci(),
	}
	for i := 0; i < 20; i++ {
		b := make([]byte, 1)
		n, err := f.Read(b)
		fmt.Printf("%d bytes read: %q. err = %v\n", n, b, err)
	}
}
```

## 7-3 函数式编程总结

例一: 斐波那契数列

例二: 为函数实现接口

例三: 使用函数来遍历二叉树

### go 语言闭包的应用

更加自然, 不需要修饰如何访问自由变量

没有 Lambda 表达式, 但是有匿名函数


## 8-1 defer 调用

资源管理与出错处理

资源管理: 打开文件要关闭, 连接数据库要释放. 一些成对出现的操作, 一旦执行的过程中出现错误, 文件关闭或资源释放的操作可能就无法正常完成, 如何保证出错的情况下依然能够正常完成关闭文件或资源释放的操作, 这就是资源管理与出错处理结合起来讲解的原因.

go 是通用 defer 调用来实现资源管理的

### defer 调用

- defer 的作用

  - 确保调用在函数结束时发生
  - 参数在 defer 语句时计算
  - defer 列表为后进先出栈

- 何时使用 defer 调用
  - Open/Close
  - Lock/Unlock
  - PrintHeader/PrintFooter


## 8-2 错误处理概念

Error 是什么东西

C:/ProgramFiles/Go/src/builtin/builtin.go:280

```go

// The error built-in interface type is the conventional interface for
// representing an error condition, with the nil value representing no error.
type error interface {
    Error() string
}

```

Error 其它类型一样, 只是一个值类型, 和 int, string 等类型都是类似的

## 8-3 服务器统一出错处理


## 8-4 panic 和 recover

### panic 
停止当前函数执行
一直向上一层调用返回, 同时执行每一层的 defer
如果没有遇见 recover, 程序就退出

panic 是一个很 "重" 的词, 要尽量去少用, 而是手动去处理各种可能的错误

### recover
仅在 defer 函数中调用
可能在 defer 中获取 panic 的值
如果无法处理, 可以重新 panic


## 8-5 服务器统一错误处理2

### error vs panic

尽量不要使用 panic, 而是使用 error

意料之中的, 能想到的可能出现的错误, 都使用 error 进行处理
如文件打不开, 用户可能输入非法的 url

意料之外的, 使用 panic. 如由于用户非法的输入导致的错误


### 错误处理综合示例

defer + panic + recover

使用 type assertion 来处理错误

函数式编程的应用


## 9-1 测试

Debugging Sucks!

Testing Rocks!

传统测试 VS 表格驱动测试

传统测试

测试数据和测试逻辑混在一起
出错信息不明确
一旦一个数据出错测试全部结束

表格驱动测试


## 10-1 并发编程

协程 Coroutine

轻量级 "线程"

"非抢占式" 多任务处理, 由协程主动交出控制权
编译器, 解释器, 虚拟机层面的多任务, 不是操作系统层面的多任务
多个协程可能在一个或多个线程上运行


### Go1.14对于非抢占式的改动
我收到很多同学反映, 这段程序我们自己运行的时候不会死机, 而是会出结果. 这说明Go语言在goroutine切换这一方面不断的在改进. 在2020年2月发布的go1.14版本中, 我注意到了这样的说法: 

Goroutines are now asynchronously preemptible. As a result, loops without function calls no longer potentially deadlock the scheduler or significantly delay garbage collection.
goroutine现在可以被异步抢占. 因此没有函数调用的循环不再对调度器造成死锁或造成垃圾回收的大幅变慢. 

也就是说Goroutine的切换现在也开始支持抢占式切换, 我的这段例子将不再造成死锁. 我试验了一下, 代码就是我视频中的: 

```go

package main

import (
    "fmt"
    "runtime"
    "time"
)

func main() {
    var a [10]int
    fmt.Println("Running in", runtime.Version())
    for i := 0; i < 10; i++ {
        go func(i int) {
            for {
                a[i]++
            }
        }(i)
    }
    time.Sleep(time.Millisecond)
    fmt.Println(a)
}

```

在Go1.13下, 结果是: 

Running in go1.13.3
然后死机了. 在我的同一台电脑上, 直至go1.13, 还是能观察到死机的效果. 

许多同学反映在自己运行的时候并不能得到死机的效果, 这是正常的, 在并发系统中, 对于goroutine的切换时机和运行结果本身就没有唯一性的保证. 加上 runtime.GOMAXPROCS(1) 强制它在一个核里运行可能更容易观察到死机. 

那么Go1.14呢: 

Running in go1.14
[2870269 3533919 3855788 2150833 0 0 0 0 0 3946632]
的确不再死机了, 立刻就出了结果. 

我在课上也说道, 虽然之前底层实现是非抢占式的, 但我们在写代码的时候其实和抢占式的切换没什么区别, 我们也不应该去依赖go语言啥时切换来实现自己的逻辑. 非抢占式只是给我们带来了一个坑, 就是上面的程序有可能死机. 不过在Go1.14之后这个坑看来被填了. 


go run goroutine.go

检测数据访问冲突, race: race condition
go run -race goroutine.go


## 10-2 go 语言的调度器

### 协程 Coroutine

subroutines are special cases of more general program components, called coroutines. In contrast to the unsymmetric

子程序是协程的一个特例

-- Donnald Knuth "The art of Computer Programming. Vol 1"

### goroutine 的定义

任何函数只需要加上 go 就能送给调度器运行

不需要在定义时区分是否是异步函数(相对Python而言)

调度器会在合适的点进行切换
  - 这也是与传统协程不同的地方, 传统协程中协程切换都需要由用户显式的写出来
  - goroutine 会进行切换

使用 go run -race xxx.go 来检测数据访问冲突


### goroutine 可能的切换点

- I/O, select

- channel

- 等待锁

- 函数调用(有时)

- runtime.Gosched()

- 以上只是参考, 不能保证切换, 也不能保证在其他地方不切换

- go 中协程运行的机制还是非抢占式的切换


## 11-1 channel

goroutine 之间通过 channel 进行通信

channel
buffered channel
range
理论基础: Communication Sequential Process(CSP)

## 11-2 使用Channel等待任务结束

Don't communicate by sharing memory, share memory by communicating.

不要通过共享内存来通信, 而是通过通信来共享内容.

在其它语言中, 如何通知一个事件完成了, 通过一个 flag, 把 flag 设置为 True, 即是通过共享内存来通信

例一, 使用 channel 来等待 goroutine 结束
例二, 使用 channel 来实现树的遍历

## 11-4 使用 select 进行调度

Select 的使用
定时器的使用
在 Select 中使用 Nil Channel

## 11-5 传统同步机制

sync.WaitGroup

sync.Mutex

sync.Cond

传统同步机制是通过共享内存来通信的, 为了线程安全要保护起来, 所以在 go 中要尽量少使用, 而是使用 channel 进行通信

## 11-6 并发编程模式

生成器

服务/任务

同时等待多个服务: 两种方法

方法一

方法二


## 11-7 并发编程模式

## 11-8 并发任务控制

- 非阻塞等待

- 超时机制

- 任务中断及退出

- 优雅退出


## 12-1 迷宫及其算法

广度优先算法

为爬虫实战项目做好准备

应用广泛, 综合性强

面试常见

迷宫

```

6 5
0 1 0 0 0
0 0 0 1 0
0 1 0 1 0
1 1 1 0 0
0 1 0 0 1
0 1 0 0 0

```

迷宫规则

1. 左上角进, 右下角出
2. 每次只能走一步
3. 只能上下左右前进, 不能沿对角线前进
4. 求出最短的走出迷宫的路径

迷宫的探索

每一个点都可以向上下左右四个方向探索

规定每一个点向外探索时按 "上左下右的逆时针" 方向进行探索

每一个点周围的所有点都按以上方向探索完成后再进行下一层的探索, 即使用广度优先算法进行探索

代码实现思路

每一个点都有三个状态, 已发现未探索, 已探索, 未被发现

广度优先探索: 把每一个 "发现未探索" 的点都先加入队列, 等上一步的点4个方向都探索完成后再探索队列中的点

第一步: (0, 0) 加入队列

第二步: (0, 0) 探索, 发现 (1, 0) 加入队列

第三步: (1, 0) 探索, 发现 (2, 0), (1, 1) 加入队列

第四步: (2, 0) 探索

(2, 0) 是一个死路, 不会发现新点

第五步: (1, 1) 探索, 发现 (1, 2) 加入队列

第六步: (1, 2) 探索, 发现 (0, 2), (2, 2) 加入队列

探索的结束条件

1. 走到了终点
2. 队列为空, 所有可探索的点都探索过了

探索的最终结果

如何从探索结果中得到最短路径

从终点开始, 找比当前点小 1 的数字, 13 > 12 > 11 ... > 1, 这样才能保证路径的唯一性和最短



## 12-2 迷宫代码实现

广度优先搜索走迷宫

用循环创建二维 slice

使用 slice 来实现队列

用 Fscanf 读取文件

对 Point 的抽象

## 13-1 http 标准库

使用 http 客户端发送请求

使用 http.Client 控制请求头等

使用 httputil 简化工作


http 服务器性能分析

import _ "net/http/pprof"

访问 localhost:8888/debug/pprof/ 可以查看服务器相关的信息

使用 go tool pprof 分析性能


```bash
# 查看 30s 内的 cpu profile
go tool pprof http://localhost:8888/debug/pprof/profile


Entering interactive mode

# 输入 web
(pprof) web

# 查看 heap 信息
go tool pprof http://localhost:8888/debug/pprof/heap

```

## 13-2 其它标准库

bufio

log

encoding/json

regexp

time

strings

math

rand

查看标准库及文档
godoc -http :8888

https://studygolang.com/pkgdoc

https://go.dev/
https://go.dev/doc/effective_go
https://go.dev/doc/
https://go.dev/doc/


## 13-3 Json 数据格式的处理

JSON 的解析

JSON 数据格式

结构体的 tag

json.marshal 与 json.unmarshal, 数据类型


## 13-4 第三方 API 数据格式的解析技巧


## 13-5 gin 框架介绍

https://github.com/gin-gonic/gin

go get -u github.com/gin-gonic/gin


middleware 的使用

context 的使用

使用 zap 来记录日志

https://github.com/uber-go/zap

go get -u go.uber.org/zap


## 14-1 爬虫项目介绍

- 为什么做爬虫项目

  - 有一定的复杂性
  - 可以灵活调整项目的复杂性
  - 平衡语言/爬虫之间的比重

go语言的爬虫库/框架
https://awesome-go.com/
https://github.com/topics/spider-framework

https://github.com/andeya/pholcus

https://github.com/PuerkitoBio/gocrawl

20k
https://github.com/gocolly/colly

1.8k
https://github.com/hu17889/go_spider

https://github.com/jaeles-project/gospider

2.1k
https://github.com/geziyor/geziyor

10k
https://github.com/crawlab-team/crawlab


- 本课程爬虫项目

  - 将不使用现成的爬虫库或框架
  - 使用 ElasticSearch 作为数据存储
  - 使用Go语言标准模板库实现http数据展示部分


- 爬虫的主题

爬取内容

  - 新闻, 博客, 社区

爬取人

  - QQ空间, 人人网, 微博, 微信, FB
  - 相亲网站, 求职网站

## 14-3 新爬虫的选择

爬取比较廉价的数据, 访问量大的网站

金融, 体育, 新闻, 产品

爱卡汽车各车型数据

项目的设计使得我们只需要增量修改

新的解析器, 新的配置

录像内容仍然以珍爱网为例

主要学习模块化, 任务管理, 调度, 分布式搭建, 接口, 函数式编程



## 14-4 总体算法

尝试人工获取数据

城市目录
https://www.zhenai.com/zhenghun

如何发现用户

城市 > 人列表 > 下一页 > 用户

用户 > 猜你喜欢

用户id自增

爬虫的实现步骤

单任务版 > 并发版 > 分布式版



## 14-5 模拟相亲网站上线啦！

本课程的实战项目是一个针对第三方网站的爬虫, 课程开设以来, 也的确遇到了一些常见的对于第三方依赖的问题: 

- 返回的网页格式变了, 导致视频里的代码不好用
- 对方网站对爬虫的过滤更严了, 我们可以采用轮换request header中的user-agent的方法, 比如[这里](https://github.com/EDDYCJY/fake-useragent), 但效果仍然有限. 

[GitHub - eddycjy/fake-useragent: A wide variety of random useragents](https://github.com/EDDYCJY/fake-useragent)

在这章之前的几节中, 我们分析了爬虫的法律风险和新爬虫的选择. 在克服这些困难的过程中, 相信同学们也学到了不少课程之外的知识. 能够把课上所学运用到一个其他的网站, 本身就好像做了个成功的大作业一样. 

但同时, 这样的做法毕竟影响了课程的连贯性, 因此本课程现在推出模拟相亲网站, 用来返回和当时的第三方网站极其类似的数据结构, 最大限度上的让视屏里的代码不作更改就可运行. 它的特点有: 

- 无限数据生成
- 资源占用小, 不吃内存
- 响应极快, 高并发
- 没有任何反爬限制, 想爬多快都行

具体用法是: 
**首先, git clone本课程最新代码**

```
git clone https://git.imooc.com/coding-180/coding-180.git
```

这里需要同学的慕课网用户名和密码登陆. 已经拉过这个代码的同学, git pull就行. 

**然后就可以起服务器了**
一定要确保自己在clone下来的代码的“根”目录下哦, 我上面的例子, 也就是这个叫coding-180目录下. 

```
coding-180$ go run mockserver/main.go
```

**访问首页**

去[http://localhost:8080](http://localhost:8080/)看看吧. 

第一步就是点击“进入”, 看看里面都有些什么用户. 

**放宽解析器对URL匹配的要求**

在视频中, 我们会在抓取用户相关URL的时候, 精确匹配 http://www.zhenai.com/… 等, 我们现在当然不能这样, 由于在模拟相亲网站上, 所有的地址都被映射为: http://localhost:8080/mock/www.zhenai.com/… 其实我们只需匹配 [www.zhenai.com](http://www.zhenai.com/) 这样的特征串即可. 具体的改动请参考 https://git.imooc.com/coding-180/coding-180/commit/1c9e644e901c6c84de99ad20c14e73c45abc06ec
不清楚也没有关系, 跟着视频走我就会讲解正则表达式, 并且在视频中边打边讲解. 

**运行爬虫**
视频中, 我们会把http://www.zhenai.com/zhenghun页面作为爬虫的起始页面, 也需要相应的改成http://localhost:8080/mock/www.zhenai.com/zhenghun, 由此我们的爬虫就可以运行了. 我们上述git clone下来的是本课程上完后的最终代码, 当然也可以直接运行. 我们的模拟网站背后的数据是无限的（仅受制于int64的大小）, 所以想要爬下来多少都行. 

想要爬快一点的话, 记得修改Config.Qps的值哦. 

**模拟相亲网站的设计和实现**
那么, 我是如何做到无限数据生成, 资源占用小, 响应极快, 高并发这些看似不可能同时实现的目标的呢? 可以看一下我的源码. 网站采用gin框架进行开发, 利用了伪随机数的概念进行数据生成, 运用了单元测试来保证生成数据的正确性, 并且注重代码规范性. 可以当作这门课程的复习使用. 

当然, 如果同学已经学习到一半, 已经有了自己可以运行的爬虫, 就不必再转到我这个模拟网站上来了. 
最后, 再又多了一种选择之后, 祝大家学习愉快！


模拟相亲网站使用方法
本模拟相亲网站可针对性响应原本发到第三方网站的请求. 
原本发送到类似 `http://www.zhenai.com/...` 的请求在本服务器 `/mock/www.zhanai.com/...` 被响应. 
响应的数据可被本课程原针对 zhenai.com 的爬虫爬取. 

例: http://www.zhenai.com/zhenghun/aomen --> http://localhost:8080/mock/www.zhenai.com/zhenghun/aomen
例: http://album.zhenai.com/u/9166010045394758737--> http://localhost:8080/mock/album.zhenai.com/u/9166010045394758737
爬虫入口请设置为:  http://localhost:8080/mock/www.zhenai.com/zhenghun
当然不要忘了我们在main.go 中配置正确的Parser. 

在视频中我们会从 http://www.zhenai.com/zhenghun 这个地址入手去分析对方网站的结构, 采用本模拟网站时, 我们只需相应的访问 http://localhost:8080/mock/www.zhenai.com/zhenghun 即可. 

不过视频中我们对于抓取数据的URL的验证过于严格. 原本我们会采用正则表达式匹配
`http://www.zhenai.com/...` 等, 现在需要放宽限制, 只需匹配 `www.zhenai.com` 等特征串即可, 不是必须要 `http://`
即把视频正则表达式中类似 `http://album.zhenai.com/u/[0-9]+` 改为 `.*album\.zhenai\.com/u/[0-9]+` 
我将在后面的章节中详细讲述有关正则表达式, 在讲到时作相应的修改便可. 
具体修改请参考 https://git.imooc.com/coding-180/coding-180/commit/1c9e644e901c6c84de99ad20c14e73c45abc06ec

## 单任务版爬虫

## 15-1 获取初始页面内容

获取并打印所有城市第一页用户的详细信息


乱码的处理

这里处理乱码的问题是珍爱网特有的, 其他网站不一定存在这样的问题, 所以不是我们做爬虫必须的. 但这段处理乱码问题的方法本省具有一定借鉴意义, 也可以对语言的编码进行加深理解, 可以选择性学习. 
而且其后获取golang.org/x/text也不用通过gopm工具. gopm工具是go语言镜像推出之前的一个过渡方案. 现在我们只要遵循第一章配置国内镜像, 并初始化go.mod, 就能简单的通过go get golang.org/x/text来获取这个代码库, 其它类似的库也是这样. 


go mod init github.com/dreamingtech/imoocgocrawler

gbk utf-8 格式转换的包
go get golang.org/x/text

检测编码的包

go get golang.org/x/net/html

## 15-2 正则表达式

获取城市名称和链接

使用 css 选择器
使用 xpath
使用正则表达式, 本课程仅使用正则表达式



## 15-3 提取城市信息



## 15-4 单任务版爬虫架构


### 解析器 Parser

输入: utf-8 编码的文本

输出: Request{URL, 对应的 Parser} 列表, Item 列表

可能在一个页面中提取到不同类型的 url, 如城市页面中提取到的城市下一页 url, 用户 url

## 15-5 Engine 与 Parser

## 15-10 单任务版爬虫性能

- 单任务版爬虫总结

  - 获取并打印所有城市第一页用户的详细信息

- 获取网页内容
  - 使用 http.Get 获取网页内容
  - 使用 Encoding 来转换编码: gbk > utf-8
  - 使用 charset.DetermineEncoding 来判断编码

- 获取城市名称和链接
  - 使用 css 选择器
  - 使用 xpath
  - 使用正则表达式
  - 本课程中仅使用正则表达式

- 解析器 Parser

  - 输入: utf-8 编码的文本
  - 输出: Request{URL, 对应的Parser}列表, Item列表


## 16-1 并发版爬虫架构



Fetcher 耗时最多

Fetcher 的输出是 Parser 的输入, 可以把 Fetcher 和 Parser 合并为一个大的 Worker 模块

抽象出 Worker 之后, 并发版爬虫就是 Worker 的并发, 很多 Worker 同时并发地发送请求, 解析响应

Worker 并发执行后, 就会出现任务的分发的问题, 多个 Request 如何分发给多个 Worker, 所以抽象出来一个 Scheduler 的模块, 把多个 Request 分发给多个 Worker 执行

而 Worker 提取到 Requests 和 Items 之后, 传递给 Engine, Engine 把 Request 提交给 Scheduler 再次排队, Engine 的 Items 入库

但注意, 并发版爬虫中, 各个模块之间就不再是通过函数的调用来组合到一起了, 各个模块之间流通的也不再是函数的参数和返回值了, 而是通过 channel 连通各个模块, 通过 channel 来进行通信和数据的传递.

Engine 和 scheduler 各使用一个 goroutine, worker 使用多个 goroutine, 它们之间通过 3 个 channel 连接

### Scheduler 的实现

1. 所有 Worker 公用一个输入

即所有 Worker 使用一个 channel 从 Scheduler 中获取数据, 抢占式的获取 Request


## 16-2 简单调度器

## 16-3 并发调度器

循环等待出现的原因及解决方法
Engine 通过函数调用把 request 提交给 Scheculer, `engine.Scheduler.Submit(r)`,
Scheduler 通过向 workerChan 中发送 request 来实现任务的分发, `s.workerChan <- request`
Scheduler 向 workerChan 中发送数据成功的前提是 有一个空闲的 worker 在等待从 workerChan 中收取 request, `request := <-in`

worker 等待从 workerChan 中收取 request 的前提是 `把上一件事情做完`, 即把上一次请求中解析到的 request 和 item 发送给 engine
engine 再调用 Scheduler 向 workerChan 中发送 request, `out <- result`

但向 workerChan 中发送请求成功的前提是要有一个空闲的 Worker, 这样就形成了一个循环等待

所以前 10 个请求发送出去之后, 程序就会陷入到循环等待中, 也就是卡死了

只需要使用 goroutine 把向 workerChan 中提交 request 的操作变成异步的, 就解决了以上问题
此时, engine 再从 out 中取数据, `result := <-out`, 提交给 Scheduler 时, `engine.Scheduler.Submit(request)`,
因为 Submit 变成了协程, 即异步的方式执行, 就不会再出现循环等待的问题了

此时的调度器就变成了并发的调度器, 通过给每个 request 创建一个 goroutine 的方式实现了并发调度. 

goroutine 只完成向 Scheduler 的 workerChan 发送 request 的工作


## 16-4 队列实现调度器

为每个 Request 创建一个 goroutine, 创建的 goroutine 完成任务后如何了, 我们也不清楚, 对程序的控制力较小.

所有的 Worker 都去抢占式的从同一个 wokerChan 中获取数据, 也没办法去控制给某个 worker 发送请求, 无法实现如负载均衡之类的功能.

Scheduler 的实现三: 把 Request 和 Worker 都放在队列中

从 Request 队列中取出头部的 Request, 再从 Worker 队列中取出空闲的 Worker, 把 Request 分发给该 Worker 去执行


## 16-5 重构和总结

## 16-6 更多城市

### 单任务版爬虫

获取并打印所有城市第一页用户的详细信息

### 获取更多页

## 16-7 更多用户与去重

- URL去重

  - 哈希表, 内存占用较大
  - 计算 md5 等哈希, 再存哈希表
  - 使用 Bloom filter 多重哈希结构
  - 使用 redis 等 key-value 存储系统实现分布式去重

## 17-1 ItemSaver 的架构

- engine 中不要做长时间的耗时操作, 获取到 request 要尽快交由 scheduler 进行处理, 获取到 item 也要尽快交由相应的模块处理

- 如何存储 item

  - 存 item 和发送请求获取响应都是耗时的网络 io 请求, 可以把获取请求, 解析, 保存 item 三个工作合并到一起, 称为一个 task 任务. Scheduler 则可以同时对 request 和 保存 item 的 item 进行调度.
- 抽象出 Task 的概念
  - FetchTask, PersistTask 共用同一个 Engine, Scheduler
  - Scheduler 同时来调度 FetchTask 和 PersistTask
  - 但是需要创建 FetchWorker, PersistWorker, 对代码的改动较大
  - 对于本项目来说, 并不是越抽象越好, FetchWorker PersistWorker 的概念对本项目就显得过重了

- 极简方案一
  - 使用 `go save(item)` 把保存 item 的操作放在协程中进行
- 极简方案二
  - 使用 `go func(){itemChan <- item}()` 把 item 放入 ItemChan 中
  - 相当于为每个 item 创建一个 goroutine, 提交给 ItemSaver, 
  - 因为 ItemSaver 的速度比 Fetcher 的速度快的多, 所以 item 和创建的 goroutine 会被快速的消费掉, 不会过多的累积

## 17-2 Docker和ElasticSearch介绍

### ElasticSearch简介
全文搜索引擎
快速地存储, 搜索和分析少量数据
用户: 维基百科, Stack Overflow, Github
应用领域: 站内搜索

### ElasticSearch 能做什么

- 存储
存储我们抓取到的数据
不需要建表, 配置字段等
json 格式的文档

- 查询
寻找: 男, 有房, 有车
寻找: 女, 年轻漂亮 > 年轻身材好 > 年轻身材指标好
原生支持, 不需要写代码, 不需要拼装查询语句

### ElasticSearch 的安装

从网站下载安装包, 安装, 配置, 运行

直接使用 Docker 运行 ES

### Docker

- 简介
容器引擎
打包/发布应用程序, 包括系统环境, 配置, 依赖
虚拟化, 沙箱机制

- 优势
解决了 "在我机器上运行的好好的"
使用 Go 语言编写

Docker入门

https://www.imooc.com/learn/867

Elastic Stack入门
https://www.imooc.com/learn/920

Docker DaoCloud 镜像
https://www.daocloud.io/

https://github.com/DaoCloud/public-image-mirror

DaoCloud 加速器
https://docs.daocloud.io/community/mirror/
https://guide.daocloud.io/dcs/docker-9153151.html
https://www.daocloud.io/mirror#accelerator-doc

docker verion
docker info

docker run hello-world
docker run -it alpine sh

docker run -d -p 80:80 nginx 

## ElasticSearch 入门

https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html

https://hub.docker.com/_/elasticsearch

2023-07-26
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.17.10
https://github.com/docker-library/elasticsearch/blob/e95712a301d6adacde7ef3d31d54dd9bca752426/7/Dockerfile

运行

docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.17.10

Make sure that Docker is allotted at least 4GiB of memory. In Docker Desktop, you configure resource usage on the Advanced tab in Preference (macOS) or Settings (Windows).

https://elk-docker.readthedocs.io/

A minimum of 4GB RAM assigned to Docker

Elasticsearch alone needs at least 2GB of RAM to run.

-e ES_JAVA_OPTS="-Xms512m -Xmx2048m" 

7.17.10 必须要限制内存的使用量, 否则在 4G 的服务器上无法运行
docker run -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms512m -Xmx2048m" docker.elastic.co/elasticsearch/elasticsearch:7.17.10

可以使用官方文档中的 7.5.2, 占用内存会小一点

docker pull docker.elastic.co/elasticsearch/elasticsearch:7.5.2
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.5.2

curl localhost:9200

```json
{
  "name" : "739b9a768759",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "9PShWs9SQAuuDr5h1HgREA",
  "version" : {
    "number" : "7.17.10",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "fecd68e3150eda0c307ab9a9d7557f5d5fd71349",
    "build_date" : "2023-04-23T05:33:18.138275597Z",
    "build_snapshot" : false,
    "lucene_version" : "8.11.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```



参考 https://blog.csdn.net/guohuan126/article/details/103904342
阿里云Centos7.5下docker中安装elasticSearch,elasticsearch-head及问题解决

测试 http 请求
https://www.jetbrains.com/help/pycharm/2023.1/http-client-in-product-code-editor.html
Tools > Http client > create request in http client



resp-api

```http
GET / HTTP/1.1
Host: localhost:9200
Content-Type: application/json
```

curl

```shell
curl --location 'http://localhost:9200' --header 'Content-Type: application/json'
```



PUT 写入数据

resp-api

```http
PUT /imooc/course/1 HTTP/1.1
Host: localhost:9200
Content-Type: application/json
Content-Length: 56

{
    "name": "golang",
    "instructor": "ccmouse"
}
```



```shell
# curl --location --request PUT 'localhost:9200/index/type/id' --header 'Content-Type: application/json'

curl --location --request PUT 'localhost:9200/imooc/course/1' \
--header 'Content-Type: application/json' \
--data '{
    "name": "golang",
    "instructor": "ccmouse"
}'
```

localhost:9200/index/type/id

index --> database

type --> table

id --> id

ES 不需要预先创建 index 和 type

type 中的数据类型可以不一致

可以使用 `_mapping` 来配置类型

响应

```json
{"_index":"imooc","_type":"course","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}
```

```json
{
    "_index": "imooc",
    "_type": "course",
    "_id": "1",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}
```



获取数据

```http
GET /imooc/course/1 HTTP/1.1
Host: localhost:9200
Content-Type: application/json
```



```shell
curl --location 'localhost:9200/imooc/course/1' \
--header 'Content-Type: application/json'
```

响应

```json
{"_index":"imooc","_type":"course","_id":"1","_version":1,"_seq_no":0,"_primary_term":1,"found":true,"_source":{
    "name": "golang",
    "instructor": "ccmouse"
}}
```



```json
{
    "_index": "imooc",
    "_type": "course",
    "_id": "1",
    "_version": 1,
    "_seq_no": 0,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "name": "golang",
        "instructor": "ccmouse"
    }
}
```

使用 post 添加数据

```http

POST /imooc/course/2 HTTP/1.1
Host: localhost:9200
Content-Type: application/json
Content-Length: 59

{
    "name": "interview",
    "instructor": "ccmouse"
}

```

```shell

curl --location 'localhost:9200/imooc/course/2' \
--header 'Content-Type: application/json' \
--data '{
    "name": "interview",
    "instructor": "ccmouse"
}'

```

响应

```json

{
    "_index": "imooc",
    "_type": "course",
    "_id": "2",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 1,
    "_primary_term": 1
}

```

使用 search 获取所有数据

```shell

curl --location 'localhost:9200/imooc/course/_search' \
--header 'Content-Type: application/json'

```

```http

GET /imooc/course/_search HTTP/1.1
Host: localhost:9200
Content-Type: application/json

```

响应

```json

{
    "took": 904,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 2,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "imooc",
                "_type": "course",
                "_id": "1",
                "_score": 1.0,
                "_source": {
                    "name": "golang",
                    "instructor": "ccmouse"
                }
            },
            {
                "_index": "imooc",
                "_type": "course",
                "_id": "2",
                "_score": 1.0,
                "_source": {
                    "name": "interview",
                    "instructor": "ccmouse"
                }
            }
        ]
    }
}
```

关键字搜索

```shell

curl --location 'localhost:9200/imooc/course/_search?q=golang&pretty=' \
--header 'Content-Type: application/json'

```

rest-api

```

GET /imooc/course/_search?q=golang&pretty= HTTP/1.1
Host: localhost:9200
Content-Type: application/json

```

resp

```json

{
  "took" : 33,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 0.6931471,
    "hits" : [
      {
        "_index" : "imooc",
        "_type" : "course",
        "_id" : "1",
        "_score" : 0.6931471,
        "_source" : {
          "name" : "golang",
          "instructor" : "ccmouse"
        }
      }
    ]
  }
}


```

添加记录, 不指定 id, 此时必须用 post, 不能使用 put

```shell

curl --location 'localhost:9200/imooc/course/?pretty=' \
--header 'Content-Type: application/json' \
--data '{
    "name": "golang-pipeline",
    "instructor": "ccmouse",
    "url": "https://www.imooc.com/learn/927"
}'

```

rest-api


```http

POST /imooc/course/?pretty= HTTP/1.1
Host: localhost:9200
Content-Type: application/json
Content-Length: 112

{
    "name": "golang-pipeline",
    "instructor": "ccmouse",
    "url": "https://www.imooc.com/learn/927"
}

```

resp

```json

{
  "_index" : "imooc",
  "_type" : "course",
  "_id" : "VDbqlIkB60J7JRB5Ol1E",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 3,
  "_primary_term" : 1
}


```

修改记录, 给 id=2 的记录加上 url

```http

POST /imooc/course/2/?pretty= HTTP/1.1
Host: localhost:9200
Content-Type: application/json
Content-Length: 114

{
    "name": "interview",
    "instructor": "ccmouse",
    "url": "https://coding.imooc.com/class/132.html"
}

```

curl

```shell

curl --location 'localhost:9200/imooc/course/2/?pretty=' \
--header 'Content-Type: application/json' \
--data '{
    "name": "interview",
    "instructor": "ccmouse",
    "url": "https://coding.imooc.com/class/132.html"
}'

```

响应

```json

{
  "_index" : "imooc",
  "_type" : "course",
  "_id" : "2",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 4,
  "_primary_term" : 1
}

```

查询数据

```shell

curl --location 'localhost:9200/imooc/course/_search/?pretty=' \
--header 'Content-Type: application/json'

```

```http

GET /imooc/course/_search/?pretty= HTTP/1.1
Host: localhost:9200
Content-Type: application/json

```

查找 132 的数据

```shell

curl --location 'localhost:9200/imooc/course/_search/?pretty=&q=132' \
--header 'Content-Type: application/json'

```

```json

{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 0.9227538,
    "hits" : [
      {
        "_index" : "imooc",
        "_type" : "course",
        "_id" : "2",
        "_score" : 0.9227538,
        "_source" : {
          "name" : "interview",
          "instructor" : "ccmouse",
          "url" : "https://coding.imooc.com/class/132.html"
        }
      }
    ]
  }
}


```

使用 mapping 来配置类型

这里请移除course
请同学们访问 http://localhost:9200/imooc/_mapping
在elasticsearch 7里面, 为了解决不同type的字段重名问题, type（也就是这里的course)概念被移除了. 具体原因请参阅 https://www.cnblogs.com/huangfox/p/9460361.html

同样的, 在代码中, https://git.imooc.com/coding-180/coding-180/src/master/crawler/persist/itemsaver.go#L53 处, ide有可能会报warning, 说type被deprecate了, 也就是被移除的意思. 但出于兼容性, 仍然允许我们在代码里写type, 但是会给出warning. 这里同学们可以不必理会, 当然也可以把这行type删掉. 

最后同学们仔细想一下在我们的课程中, 其实type的出现并没有起到什么实质性的作用. 现在被官方移除了也具有合理性. 


ES - es为什么要移除type? 

https://www.cnblogs.com/huangfox/p/9460361.html


1、index、type的初衷

之前es将index、type类比于关系型数据库（例如mysql）中database、table, 这么考虑的目的是“方便管理数据之间的关系”. 

2、为什么现在要移除type? 

2.1 在关系型数据库中table是独立的（独立存储）, 但es中同一个index中不同type是存储在同一个索引中的（lucene的索引文件）, 因此不同type中相同名字的字段的定义（mapping）必须一致. 

2.2 不同类型的“记录”存储在同一个index中, 会影响lucene的压缩性能. 

3、替换策略

3.1 一个index只存储一种类型的“记录”

这种方案的优点：

a）lucene索引中数据比较整齐（相对于稀疏）, 利于lucene进行压缩. 

b）文本相关性打分更加精确（tf、idf, 考虑idf中命中文档总数）

3.2 用一个字段来存储type

如果有很多规模比较小的数据表需要建立索引, 可以考虑放到同一个index中, 每条记录添加一个type字段进行区分. 

这种方案的优点：

a）es集群对分片数量有限制, 这种方案可以减少index的数量. 


4、迁移方案

之前一个index上有多个type, 如何迁移到3.1、3.2方案? 

4.1 先针对实际情况创建新的index, [3.1方案]有多少个type就需要创建多少个新的index, [3.2方案]只需要创建一个新的index. 

4.2 调用_reindex将之前index上的数据同步到新的索引上. 

 

5、参考

a）Removal of mapping types 


mapping 即关系型数据库中的 "表结构"

```shell

curl --location 'localhost:9200/imooc/_mapping?pretty=' \
--header 'Content-Type: application/json'

```

```json
{
  "imooc" : {
    "mappings" : {
      "properties" : {
        "instructor" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "name" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "url" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        }
      }
    }
  }
}

```

elasticsearch ik 插件进行分词
https://github.com/medcl/elasticsearch-analysis-ik

https://segmentfault.com/a/1190000039018379

Elasticsearch IK 分词器插件的安装使用

#### 安装

下载地址：[GitHub - medcl/elasticsearch-analysis-ik: The IK Analysis plugin integrates Lucene IK analyzer into elasticsearch, support customized dictionary.](https://github.com/medcl/elasticsearch-analysis-ik/)

解压到插件目录 your-es-root/plugins/ik/ 即可。

重启 ES 服务器，观察加载日志是否包含 IK 分词器。

#### IK 分词器的使用

IK 分词器有两种分词模式：

- ik_max_word：最细粒度拆分，即最大词数。
- ik_smart：最粗粒度拆分，即最小词数。

```routeros
GET _analyze
{
  "analyzer": "ik_max_word",
  "text": "中华人民共和国人民大会堂"
}
```

结果为：中华人民共和国、中华人民、中华、华人、人民共和国、人民、共和国、大会堂、大会、会堂等。

```routeros
GET _analyze
{
  "analyzer": "ik_smart",
  "text": "中华人民共和国人民大会堂"
}
```

结果为：中华人民共和国、人民大会堂。

#### 最佳实践

两种分词器使用的最佳实践是：索引时使用ik_max_word，搜索时使用ik_smart。
即：索引时最大化的将储存的内容进行分词，搜索时更精确的搜索到想要的结果。

例如用户输入“华为手机”，用户的想法是想搜索出“华为手机”的商品，而不是华为的其它商品或者其他品牌的手机，也就是商品信息中必须只有华为手机这个词。

此时使用`ik_smart`和`ik_max_word`都会将”华为手机“拆分为”华为“和”手机“两个词，那些只包括“华为”这个词的信息也会被搜索出来，没有满足用户的目标。

这种情况可以将“华为手机”添加到自定义词库，添加后两个分词器的效果为：

- ik_max_word：华为手机、华为、手机
- ik_smart：华为手机

这时候在索引时使用 ik_max_word，搜索时用 ik_smart 即可实现目标。

如果想将包含“华为”这个词的信息也搜索出来，输入 “华为 华为手机”，就会将包含华为、华为手机的信息都搜索出来。

#### 增加自定义分词

```awk
vim your-es-root/plugins/ik/config/IKAnalyzer.cfg.xml

<entry key="ext_dict">my.dic</entry>
vim your-es-root/plugins/ik/config/my.dic

华为手机
```

重启 ES 服务器，观察加载日志是否包含自定义分词配置文件。



Elastic Search 初识总结

- 使用 REST 接口
  - http GET, PUT, POST + url 为名词, 组成一个 REST 接口
- PUT/POST 创建/修改数据, 使用 POST 可省略 id
- GET 获取数据
- GET `<index>/<type>/_search?q=` 来进行全文搜索

## 17-5 向 Elasticsearch 存储数据

elasticsearch 客户端

https://github.com/elastic/go-elasticsearch
https://github.com/olivere/elastic
go get github.com/olivere/elastic/v7



在服务器上运行测试文件

cd /data/dev/go/imoocgocrawler/persist

go test

&{dating_profile zhenai VzbrmYkB60J7JRB5w124 1 created 0xc0000a4180 2 1 0 false}
PASS
ok      github.com/dreamingtech/imoocgocrawler/persist  0.017s



添加 ssh terminal

Settings > Tools > SSH Terminal > SSH configuration

使用

Terminal > 下拉箭头 > New Predefined Session ubuntu@xxx.xxx

参考: https://www.cnblogs.com/hi3254014978/p/16341816.html

使用 +v 打印 elasticsearch 的响应

resp: &{Index:dating_profile Type:zhenai Id:WDbymYkB60J7JRB5LF2K Version:1 Result:created Shards:0xc00006a1c0 SeqNo:3 PrimaryTerm:1 Status:0 ForcedRefresh:false}PASS
ok      github.com/dreamingtech/imoocgocrawler/persist  0.025s

使用 #v 打印 elasticsearch 的响应

```

resp: &elastic.IndexResponse{Index:"dating_profile", Type:"zhenai", Id:"WTb0mYkB60J7JRB5L103", Version:1, Result:"created", Shards:(*elastic.ShardsInfo)(0xc00006ac40), SeqNo:4, PrimaryTerm:1, Status:0, ForcedRefresh:false}PASS
ok      github.com/dreamingtech/imoocgocrawler/persist  0.018s

```

查询 es 中保存的数据

```shell

curl --location 'localhost:9200/dating_profile/zhenai/_search/?pretty=' \
--header 'Content-Type: application/json'

```

```json

{
  "took" : 139,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 5,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "dating_profile",
        "_type" : "zhenai",
        "_id" : "VTbqmYkB60J7JRB5Ll0u",
        "_score" : 1.0,
        "_source" : {
          "name" : "安静的雪",
          "gender" : "女",
          "age" : 34,
          "height" : 162,
          "weight" : 57,
          "income" : "3001-5000元",
          "marriage" : "离异",
          "education" : "大学本科",
          "occupation" : "人事/行政",
          "hokou" : "山东菏泽",
          "xingzuo" : "牡羊座",
          "house" : "已购房",
          "car" : "未购车"
        }
      },
      {
        "_index" : "dating_profile",
        "_type" : "zhenai",
        "_id" : "VjbrmYkB60J7JRB5SF1i",
        "_score" : 1.0,
        "_source" : {
          "name" : "安静的雪",
          "gender" : "女",
          "age" : 34,
          "height" : 162,
          "weight" : 57,
          "income" : "3001-5000元",
          "marriage" : "离异",
          "education" : "大学本科",
          "occupation" : "人事/行政",
          "hokou" : "山东菏泽",
          "xingzuo" : "牡羊座",
          "house" : "已购房",
          "car" : "未购车"
        }
      },
      {
        "_index" : "dating_profile",
        "_type" : "zhenai",
        "_id" : "VzbrmYkB60J7JRB5w124",
        "_score" : 1.0,
        "_source" : {
          "name" : "安静的雪",
          "gender" : "女",
          "age" : 34,
          "height" : 162,
          "weight" : 57,
          "income" : "3001-5000元",
          "marriage" : "离异",
          "education" : "大学本科",
          "occupation" : "人事/行政",
          "hokou" : "山东菏泽",
          "xingzuo" : "牡羊座",
          "house" : "已购房",
          "car" : "未购车"
        }
      },
      {
        "_index" : "dating_profile",
        "_type" : "zhenai",
        "_id" : "WDbymYkB60J7JRB5LF2K",
        "_score" : 1.0,
        "_source" : {
          "name" : "安静的雪",
          "gender" : "女",
          "age" : 34,
          "height" : 162,
          "weight" : 57,
          "income" : "3001-5000元",
          "marriage" : "离异",
          "education" : "大学本科",
          "occupation" : "人事/行政",
          "hokou" : "山东菏泽",
          "xingzuo" : "牡羊座",
          "house" : "已购房",
          "car" : "未购车"
        }
      },
      {
        "_index" : "dating_profile",
        "_type" : "zhenai",
        "_id" : "WTb0mYkB60J7JRB5L103",
        "_score" : 1.0,
        "_source" : {
          "name" : "安静的雪",
          "gender" : "女",
          "age" : 34,
          "height" : 162,
          "weight" : 57,
          "income" : "3001-5000元",
          "marriage" : "离异",
          "education" : "大学本科",
          "occupation" : "人事/行政",
          "hokou" : "山东菏泽",
          "xingzuo" : "牡羊座",
          "house" : "已购房",
          "car" : "未购车"
        }
      }
    ]
  }
}

```



每页返回 100 条数据

```shell
curl --location 'localhost:9200/dating_profile/zhenai/_search/?pretty=&size=100' \
--header 'Content-Type: application/json'
```



elastic search 7 查询出错

```shell

curl localhost:9200/dating_profile/zhenai/_search?pretty=&q=男 已购房 已购车
已购房: command not found

```

使用 elstic search 5

https://hub.docker.com/_/elasticsearch/tags?page=1&name=5.6

```shell
docker pull elasticsearch:5.6
docker run -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  -e ES_JAVA_OPTS="-Xms512m -Xmx1536m" elasticsearch:5.6

curl --location '162.62.134.181:9200/dating_profile/zhenai/_search?q=%E7%94%B7%20%E5%B7%B2%E8%B4%AD%E6%88%BF%20%E5%B7%B2%E8%B4%AD%E8%BD%A6' \
--header 'Content-Type: application/json'

# GET 162.62.134.181:9200/dating_profile/zhenai/_search?pretty=true&size=100&q=男 已购房 已购车
# Content-Type*: application/json

GET /dating_profile/zhenai/_search?q=男 已购房 已购车 HTTP/1.1
Host: 162.62.134.181:9200
Content-Type: application/json
```

查询 有房 有车 年龄小于 30的 男人

```http
GET /dating_profile/zhenai/_search?q=男 有房 有车 age:(<30) HTTP/1.1
Host: 162.62.134.181:9200
Content-Type: application/json
```



```shell
curl --location '162.62.134.181:9200/dating_profile/zhenai/_search?q=%E7%94%B7%20%E6%9C%89%E6%88%BF%20%E6%9C%89%E8%BD%A6%20age%3A(%3C30)' \
--header 'Content-Type: application/json'
```



## 17-7 添加 URL 与 ID



通过 id 查询 ES 中的数据

```shell

curl --location '162.62.134.181:9200/dating_profile/zhenai/1991290594400503513' \
--header 'Content-Type: application/json'

```



```http
GET /dating_profile/zhenai/1991290594400503513 HTTP/1.1
Host: 162.62.134.181:9200
Content-Type: application/json
```



## 17-8 重构与运行

Postman 查询

162.62.134.181:9200/dating_profile/zhenai/_search?q=Payload.gender:(=男) 有房 有车 Payload.age:(<30)



```shell
curl --location '162.62.134.181:9200/dating_profile/zhenai/_search?q=Payload.gender%3A(%3D%E7%94%B7)%20%E6%9C%89%E6%88%BF%20%E6%9C%89%E8%BD%A6%20Payload.age%3A(%3C30)' \
--header 'Content-Type: application/json'
```





```http
GET /dating_profile/zhenai/_search?q=Payload.gender:(=男) 有房 有车 Payload.age:(<30) HTTP/1.1
Host: 162.62.134.181:9200
Content-Type: application/json
```



## 17-9 标准模板库介绍

### 前端数据展示 MVC 结构

### html/template

### 模板引擎

服务端生成最终网页

静态页面, 没有异步等功能

适合做后台或者维护页面

### 方法

取值

循环

选择

函数调用

## 17-10 实现前端展示页面

设置 starter.go 端口为 3306，访问以下地址

http://162.62.134.181:3306/search?q=%E7%94%B7%20%E5%B7%B2%E8%B4%AD%E6%88%BF&from=20

http://162.62.134.181:3306/search?q=男%20已购房&from=20

http://162.62.134.181:3306/search?q=%E7%94%B7+%E5%B7%B2%E8%B4%AD%E6%88%BF+Payload.Age%3A%28%3C30%29



/search?q=男+已购房+Payload.Age%3A%28<30%29

男 已购房 Payload.Age:(<30)

重写查询字符串后的搜索



http://162.62.134.181:3306/search?q=%E7%94%B7+%E5%B7%B2%E8%B4%AD%E6%88%BF+Age%3A%28%3C30%29+Height%3A%28%3E180%29

search?q=男+已购房+Age%3A%28<30%29+Height%3A%28>180%29

男 已购房 Age:(<30) Height:(>180)

Gender:(=男) House:(=有房) Age:(<30) Height:(>180)

http://162.62.134.181:3306/search?q=Gender%3A%28%3D%E7%94%B7%29+House%3A%28%3D%E6%9C%89%E6%88%BF%29+Age%3A%28%3C30%29+Height%3A%28%3E180%29



Gender:(=女) Age:(<30) Height:(>165) Weight:([1 TO 50])

http://162.62.134.181:3306/search?q=Gender%3A%28%3D%E5%A5%B3%29+Age%3A%28%3C30%29+Height%3A%28%3E165%29+Weight%3A%28%5B1+TO+50%5D%29

## 17-11 完善前端展示

搜索字符串重写

翻页

使用 http.FileServer 来提供静态内容, css, js, 图片, 首页等



## 18-1 分布式系统

对分布式系统的要求, 功能和特点

- 多个节点
- 消息传递
- 完成特定需求

多个节点

- 容错性
- 可扩展性(性能)
- 固有分布性

消息传递

- 节点具有私有存储
- 易于开发
- 可扩展性(功能)
- 对比: 并行计算

消息传递的方法

- REST api 接口
- RPC 远程过程调用
- 中间件, 可以非即时性通信

### 一般消息传递的方法

- 对外: REST

- 模块内部: RPC
- 模块之间: 中间件, REST

### 分布式架构 VS 微服务架构

分布式: 指导节点之间如何进行通信

微服务: 鼓励按业务越划分模块

微服务架构通过分布式架构来实现

### 多层架构 VS 微服务架构

微服务架构具有更多的 "服务"

微服务通常需要配合自动化测试, 部署, 服务发现等

目前我们倾向于使用微服务架构

## 18-2 分布式爬虫架构

### 目前的问题

- 限流问题
  - 单节点能够承受的流量有限
  - 将 Worker 放到不同的节点, Request 通过网络发送给其它节点上的 Worker

- 去重问题

  - 单节点能够承受的去重数据量有限
  - 无法保存之前的去重结果
  - 基于 key-value store, 如 redis 进行分布式去重

  - worker 进行去重

- 数据存储问题

  - 存储部分的结构, 技术栈和爬虫部分的区别很大
  - 进一步优化需要特殊的, 具有 ElasticSearch 技术背景的人才
  - 固有分布式

### 本课程架构

解决限流问题(理论上)

解决存储问题

分布式去重: 课下作业

#### 从 channel 到分布式

分布式实现的关键

为什么要把一件事情通过 channel 交给 goroutine 去做呢? 

因为这件事做的比较慢

### channel 到分布式实现方式一: RPC

直接在 goroutine 中通过 rpc 同步调用远程服务

#### RPC不同实现

jsonrpc (本课程采用)

grpc (使用 protobuf)

Thrift (facebook出品)

### channel 到分布式实现二: 通过网络传递 channel 数据流

原来是 goroutine 通过 channel 把数据传递给其它 goroutine

现在是 goroutine 把数据交给 client, client 把数据交给 server, server 再交给分布式的 goroutine 

#### 自有协议

docker/libchan

NATS streaming

gocircuit

或自己实现, 如免费课中 < 使用 channel 搭建分布式处理管道 >

### channel 到分布式实现二: 消息中间件

## 18-3 jsonrpc 的使用



```shell
telnet localhost 1234
# Trying 127.0.0.1...
# Connected to localhost.
# Escape character is '^]'.
{"method":"abc.def"}
# {"id":null,"result":null,"error":"rpc: can't find service abc.def"}

{"method":"DemoService.Div","params":[{"A":3,"B":4}],"id":1}
# {"id":1,"result":0.75,"error":null}

{"method":"DemoService.Div","params":[{"A":3,"B":0}],"id":1234}
# {"id":1234,"result":null,"error":"division by zero"}

{"method":"DemoService.Div","params":[{"A":3,"B":"a"}],"id":1234}
# {"id":1234,"result":null,"error":"json: cannot unmarshal string into Go struct field Args.B of type int"}

```

