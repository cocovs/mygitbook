# Golang 学习笔记

### 包



#### 字符串转换

- `import "strconv"`

包strconv实现了对基本数据类型的字符串表示的转换。

 ParseBool，ParseFloat，ParseInt 和 ParseUint 将字符串转换为值： 

```javascript
b, err := strconv.ParseBool("true")
f, err := strconv.ParseFloat("3.1415", 64)
i, err := strconv.ParseInt("-42", 10, 64)
u, err := strconv.ParseUint("42", 10, 64)
```

 解析函数返回最宽的类型（float64，int64和uint64）



 FormatBool，FormatFloat，FormatInt 和 FormatUint将值转换为字符串： 

```javascript
s := strconv.FormatBool(true)
s := strconv.FormatFloat(3.1415, 'E', -1, 64)
s := strconv.FormatInt(-42, 16)
s := strconv.FormatUint(42, 16)
```

 AppendBool，AppendFloat，AppendInt 和 AppendUint 是相似的，但将格式化后的值附加到目标切片。 

####  log日志

import "log"

log包实现了简单的日志服务。本包定义了Logger类型，该类型提供了一些格式化输出的方法。本包也提供了一个预定义的“标准”Logger，可以通过辅助函数Print[f|ln]、Fatal[f|ln]和Panic[f|ln]访问，比手工创建一个Logger对象更容易使用。Logger会打印每条日志信息的日期、时间，默认输出到标准错误。Fatal系列函数会在写入日志信息后调用os.Exit(1)。Panic系列函数会在写入日志信息后panic。



func (*Logger) Fatal

```
func (l *Logger) Fatal(v ...interface{})
```

Fatal等价于{l.Print(v...); os.Exit(1)}

log.Fatal函数完成：

1. 打印输出内容
2. 退出应用程序
3. defer函数不会执行



####  bytes

```
import "bytes"
```

bytes包实现了操作[]byte的常用函数。本包的函数和strings包的函数相当类似。



**func NewReader**

```
func NewReader(b []byte) *Reader
```

NewReader创建一个从b读取数据的Reader。



#### http

http包提供了HTTP客户端和服务端的实现。

Get、Head、Post和PostForm函数发出HTTP/ HTTPS请求。



**func [NewRequest](https://github.com/golang/go/blob/master/src/net/http/request.go?name=release#479)**

```
func NewRequest(method, urlStr string, body io.Reader) (*Request, error)
```

NewRequest使用指定的方法、网址和可选的主题创建并返回一个新的*Request。

如果body参数实现了io.Closer接口，Request返回值的Body 字段会被设置为body，并会被Client类型的Do、Post和PostFOrm方法以及Transport.RoundTrip方法关闭。



####  os

**var Args**

```
var Args []string
```

Args保管了命令行参数，第一个是程序名。

```
fmt.Println("args[0]: ", os.Args[0])
fmt.Println("osargs[1]: ", os.Args[1])
```

![image-20220509142930122](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220509142930122.png)





####  ioutil

```go
func ReadAll(r io.Reader) ([]byte, error)

// ReadFile 读取文件中的所有数据，返回读取的数据和遇到的错误。
// 如果读取成功，则 err 返回 nil，而不是 EOF
```





####  json

json包实现了json对象的编解码，参见[RFC 4627](http://tools.ietf.org/html/rfc4627)。Json对象和go类型的映射关系请参见Marshal和Unmarshal函数的文档。



**func Marshal**

```
func Marshal(v interface{}) ([]byte, error)
```

Marshal函数返回v的json编码。

**func Unmarsha**

```
func Unmarshal(data []byte, v interface{}) error
```

Unmarshal函数解析json编码的数据并将结果存入v指向的值。





####  fmt

**func Fprintf**

```
func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error)
```

Fprintf根据format参数生成格式化的字符串并写入w。返回写入的字节数和遇到的任何错误。

**func Println**

```
func Println(a ...interface{}) (n int, err error)
```

Println采用默认格式将其参数格式化并写入标准输出。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。返回写入的字节数和遇到的任何错误。

**func Sprintf**

```
func Sprintf(format string, a ...interface{}) string
```

Sprintf根据format参数生成格式化的字符串并返回该字符串。



**fmt.Scan**

```go
func Scan(a ...interface{}) (n int, err error)
```









####  builtin

**func append**

```go
func append(slice []Type, elems ...Type) []Type
```

内建函数append将元素追加到切片的末尾。若它有足够的容量，其目标就会重新切片以容纳新的元素。否则，就会分配一个新的基本数组。append返回更新后的切片，因此必须存储追加后的结果。

**func copy**

```
func copy(dst, src []Type) int
```

内建函数copy将元素从来源切片复制到目标切片中，也能将字节从字符串复制到字节切片中。copy返回被复制的元素数量，它会是 len(src) 和 len(dst) 中较小的那个。来源和目标的底层内存可以重叠。

**func delete**

```
func delete(m map[Type]Type1, key Type)
```

内建函数delete按照指定的键将元素从映射中删除。若m为nil或无此元素，delete不进行操作。





####  time

time包提供了时间的显示和测量用的函数。日历的计算采用的是公历。

**type Time**

```
type Time struct {
    // 内含隐藏或非导出字段
}
```

Time代表一个纳秒精度的时间点。

程序中应使用Time类型值来保存和传递时间，而不能用指针。就是说，表示时间的变量和字段，应为time.Time类型，而不是*time.Time.类型。

**func Now**

```
func Now() Time
```

Now返回一个 `Time`类型，当前本地时间。

**func Unix**

```
func Unix(sec int64, nsec int64) Time
```

Unix创建一个本地时间，对应sec和nsec表示的Unix时间（从January 1, 1970 UTC至该时间的秒数和纳秒数）。

nsec的值在[0, 999999999]范围外是合法的。

Unix将t表示为Unix时间，即从时间点January 1, 1970 UTC到时间点t所经过的时间（单位秒）

```go
2022-05-03 17:37:42.6907493 +0800 CST m=+0.003058201
1651570662709720400
```



 **type Ticker**
```
type Ticker struct {
    C <-chan Time // 周期性传递时间信息的通道
    // 内含隐藏或非导出字段
}
```

Ticker保管一个通道，并每隔一段时间向其传递"tick"。

 **func NewTicker**

```
func NewTicker(d Duration) *Ticker
```

NewTicker返回一个新的Ticker，该Ticker包含一个通道字段，并会每隔时间段d  就向该通道发送当时的时间。它会调整时间间隔 或者 丢弃 tick 信息以适应反应慢的接收者。如果 d<=0 会 panic 。关闭该 Ticker 可以释放相关资源。







####  regexp

regexp包实现了正则表达式搜索。

正则表达式采用RE2语法（除了\c、\C），和Perl、Python等语言的正则基本一致。

**Match**

```
func Match(pattern string, b []byte) (matched bool, err error)
```

Match检查b中是否存在匹配pattern的子序列。更复杂的用法请使用Compile函数和Regexp对象。

**MatchString**

```
func MatchString(pattern string, s string) (matched bool, err error)
```














### 报错以及问题

#### **问题：**

​	**配置GO：**

在

```
export GOROOT=/usr/local/go
export GOPATH=/home/ubuntu/gopath
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

go env -w GOPROXY=https://goproxy.cn,direct





#### **报错：**

`ERROR：go run: cannot run *_test.go files` ：`*_test.go`是`golang`特有的约定，为测试文件，一般不要命名为该格式。

 在go get 之后，package 已经下载在 $GOPATH/pkg/mod 下。但使用 VSCode 写 Go 时仍然出现 `can not import find [...] in any of [...] 报错 `   报错但是依然可以编译运行，vscode只打开一个项目，不在报错。

提示**rune 文字中的字符数过多**：单引号换成漂号  **\``**

![image-20220509011928314](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220509011928314.png)

### 细节

**注释**

Golang中**注释**类型：
Go支持c语言风格的`/**/`块注释，也支持c++风格的`//`行注释。行注释更通用，块注释主要用于针对包的详细说明或者屏蔽大块的代码

1. 行注释 `//`     VSCode快捷键：`ctrl+/`  再按一次取消注释
2. 块注释（多行注释） `/**/ `     VSCode快捷键：`shift+alt+a` 再按一次取消注释
            注意：块注释中不可以嵌套块注释

**标准库函数源码**

Golang 的函数源码在golang安装目录 `src`文件夹下：

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220430113053087.png" alt="image-20220430113053087" style="zoom:67%;" />



标准库API文档查看：Golang中文网在线标准库文档: https://studygolang.com/pkgdoc

### 变量与数据类型

**变量**

作为静态类型语言，go 变量总是有固定的数据类型，类型决定了变量内存的长度和存储格式，只能修改变量值，无法改变类型（类型转换和指针操作只是修改了变量值，并非改变类型

关键字 var 用于定义变量，和 c 不同，类型放在变量名后。显示提供初始值，可以省略变量类型，由编译器推断。

```go
package main
import "fmt"

func main(){
	var age int // 创建变量
	age  = 10
	fmt.Println("age = ",age);

	var age1 int = 20;//创建变量并赋值
	fmt.Println("age1 = ",age1);

	//var num int = 12.1
       //如果赋值类型与变量类型不同会报错
	//fmt.Println("num = ",num);
}
```

编译：

```
age = 10
age1 = 20
num = 12
```

![image-20220430190431159](Golang 学习笔记.assets/image-20220430190431159.png)

ps:控制台输入`go run test_1.go ` 可以直接运行



不同类型初始化：

```
var a, s = 100, "abc"
```

简短模式：

除使用var关键字，还可以使用更简短的变量定义和初始化

```
func main(){
	x:= 100
	a, s := 1, "abc"
}
```

但简短模式：

* 定义变量，同时显式初始化
* 不能提供数据类型
* 只能用在函数内部：当是用简短模式想要修改全局变量时，会重新定义和初始化同名局部变量



测试代码：

```
package main

import "fmt"

//全局变量：定义在函数外的变量
var n7 = 100
var n8 = 9.3

//设计者认为上面的全局变量的写法太麻烦，可以一次声明：
var (
	n9  = 500
	n10 = "netty"
)

func main() {
	//定义在{}中的变量叫做局部变量

	//变量的使用方法：
	//指定变量的类型，并且赋值，
	var num0 int = 18
	fmt.Println("指定类型并赋值：  ", num0)

	//指定类型，不赋值，使用默认值
	var num1 int
	fmt.Println("指定类型，不赋值：", num1)

	//不写类型，根据赋值进行判断变量的类型（自动类型判断
	var num2 = "tom"
	fmt.Println("自动判断变量类型： ", num2)

	//省略var
	sex := "男"
	fmt.Println("省略var: ", sex)

	fmt.Println("-------------------------------------------")

	//声明多个变量
	var n1, n2, n3 int
	fmt.Println("n1 ", n1)
	fmt.Println("n2 ", n2)
	fmt.Println("n3 ", n3)

	fmt.Println("多次声明变量：")
	var n4, name, n5 = 10, "jack", 7.7
	fmt.Println(n4)
	fmt.Println(name)
	fmt.Println(n5)

	n6, height := 6.1, 100.5
	fmt.Println(n6)
	fmt.Println(height)

	fmt.Println("------------------全局变量-------------------")
	fmt.Println(n7)
	fmt.Println(n8)

	fmt.Println(n9)
	fmt.Println(n10)

}
```

输出：

```
指定类型并赋值：   18
指定类型，不赋值： 0
自动判断变量类型：  tom
省略var:  男
6.1
100.5
------------------全局变量-------------------
100
9.3
500
netty
```



注意：在 go 中，未使用局部变量将会报错

强制类型转换：



变量的数据类型：

![image-20220430205239872](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220430205239872.png)

**常量**

常量在运行时恒定不变的值，未使用的常量不会引发编译错误。

```go
package main

import "fmt"

func main() {

	const x = 123
	println(x)
	const y = 1.23

	//不同作用域定义同名变量
	{
		const x = "abc"
		println(x)
	}

	//常量组中不指定类型和初始值，则与上一行非空常量右值相同
	const (
		a uint16 = 120
		b
		d = "abc"
		e
	)
	fmt.Printf("%T, %v\n", b, b)//输出类型和值
	fmt.Printf("%T, %v\n", e, e)
}

```

输出：

```
123
abc
uint16, 120
string, abc
```

#### 数据类型

![image-20220501111651668](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220501111651668.png)

![image-20220501111705047](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220501111705047.png)

**整数**

golang的整数默认为int类型

有符号整数：

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220430222459748.png" alt="image-20220430222459748" style="zoom:80%;" />

无符号整数：

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220430222652365.png" alt="image-20220430222652365" style="zoom:80%;" />

其他整数类型：

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220430222736007.png" alt="image-20220430222736007" style="zoom: 80%;" />

测试变量所占用字节数：

```go
//测试整数各个类型所占字节数
package main

// import "fmt"
// import "unsafe"
import (
	"fmt"
	"unsafe"
)

var n0 int
var n1 int8
var n2 int16
var n3 int32
var n4 int64

func main() {
	fmt.Println("int.size() ", unsafe.Sizeof(n0))
	fmt.Println("int8.size() ", unsafe.Sizeof(n1))
	fmt.Println("int16.size() ", unsafe.Sizeof(n2))
	fmt.Println("int32.size() ", unsafe.Sizeof(n3))
	fmt.Println("int64.size() ", unsafe.Sizeof(n4))	
}
```



输出：

```
int.size()  8
int8.size()  1
int16.size()  2
int32.size()  4
int64.size()  8
```

**浮点类型**

![image-20220501002720915](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220501002720915.png)





```
//浮点类型
package main

import (
	"fmt"
)

func main() {
	//定义浮点类型数字
	var n0 float32 = 3.14
	fmt.Println("floate32: ",n0)
	
	//科学计数法表示负浮点数,E/e 都可以表示
	var n1 float32 = -3.14
	fmt.Println("floate32 -3.14: ", n1)
	
	//科学计数法表示：
	var n2 float32 = 314E-2
	fmt.Println("floate32 314E-2:  ", n2)
	
	var n3 float32 = 314E+2
	fmt.Println("floate32 413E+2 ", n3)
	
	var n4 float32 = 314e-2
	fmt.Println("float32 314e-2 ", n4)

	//出现精度损失，长位浮点数建议使用 float32
	var n5 float32 = 256.000000916
	fmt.Println("float32 256.000000916: ", n5)
	var n6 float64 = 256.000000916
	fmt.Println("float64 256.000000916: ", n6)
}

```



输出：

```
floate32:  3.14
floate32 -3.14:  -3.14
floate32 314E-2:   3.14
floate32 413E+2  31400
float32 314e-2  3.14
float32 256.000000916:  256
float64 256.000000916:  256.000000916
```

**字符类型**

Golang中没有专门的字符类型，如果要存储单个字符(字母)，一般使用byte来保存。

Golang的字符对应的使用的是UTF-8编码（Unicode是对应的字符集，UTF-8是Unicode的其中的一种编码方案）

![image-20220501004128191](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220501004128191.png)



```go
package main

import "fmt"

func main() {
	var n0 int = '中'
	fmt.Println("n0 中: ", n0)

	var n1 byte = 'a'
	fmt.Println("n1 a: ", n1)

	var n2 byte = n1 + 1
	fmt.Println("n2 n1+1: ", n2)

	//格式化输出字符
	fmt.Printf("n0: %c\n", n0)
	fmt.Printf("n1: %c\n", n1)
	fmt.Printf("n2: %c\n", n2)

}

```

输出：

```
n0 中:  20013
n1 a:  97
n2 n1+1:  98
n0: 中
n1: a
n2: b
```



![image-20220501005209074](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220501005209074.png)

**布尔类型**

布尔类型也叫bool类型，只允许取值 ture 或者 false，占用1个字节

测试：

```go
//测试bool类型
package main

import "fmt"

func main() {
	var flag0 bool = true
	fmt.Println("this is true: ", flag0)

	var flag1 bool = false
	fmt.Println("this is false: ", flag1)

	var flag2 bool = 1 < 2
	fmt.Println("1 < 2 ? : ", flag2)
}
```

输出：

```
this is true:  true
this is false:  false
1 < 2 ? :  true
```

**字符串类型**

字符连接而成的字符序列

类型名 string

测试：

```go
```

输出：

**指针**

指针变量存放目标内存的地址。 

使用 `*` 符号得到地址。

使用`*`符号得到目标值（并可修改）

* 指针变量接受的必须为地址值
* 地址值必须匹配，比如存放int的指针的匹配值也必须为int

```go
package main

import "fmt"

func main() {
	var n0 int = 1
	var ptr *int = &n0
	fmt.Println("n0的地址为： ", ptr)
}

```

```
n0的地址为：  0xc000014088
```

**关键字 预定义标识符**

![image-20220501172811278](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220501172811278.png)

### 运算符

**算术运算符：**`+ ，-，*，/，%，++，--`

++ -- 自增，自减操作只能在单独使用，不能参与运算

**赋值运算符:**=,+=，-=，*=，/=,%=

**关系运算符:**==,!=,>,<,> =，<=

* 关系运算符的结果都是bool型，也就是要么是true，要么是false

**逻辑运算符:** &&(逻辑与/短路与)，||（逻辑或/短路或），!（逻辑非）

![image-20220501220651103](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220501220651103.png)

#### 运算符优先级表

![image-20220501173711231](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220501173711231.png)

### 流程控制

**if分支**

if后跟条件表达式，当为true时执行{}，当为false时不执行。

在golang里，if后面可以并列的加入变量的定义，使用简短模式定义 `:=`

在多分支下，使用 `else if`，但必须跟在前一个`}`后

测试：

```go
package main

import "fmt"

func main() {
 
	if n0 := 88; n0 > 100 {
		fmt.Println("1111")
	} else if n0 > 90 {
		fmt.Println("2222")
	} else if n0 > 80 {
		fmt.Print("3333")
	}

}
```

输出：

```
3333
```

**switch分支**

switch后是一个表达式(常量值、变量、一个有返回值的函数)

case后面的值如果是常量值(字面量)，则要求不能重复

分支还可以使用表达式，这时候switch语句后面不需要再跟判断变量。

switch穿透，利用fallthrough关键字，如果在case语句块后增加fallthrough ,则会继续执行下一个case。

default 用于当所有case条件都不匹配情况下，执行default块（并且位置无所谓，编译器不会先执行default)。



```
package main

import "fmt"

func main() {
	var n0 int = 8
	switch n0 {
	case 10, 9:
		fmt.Println("10 or 9")
	case 8:
		fmt.Println("8")
		fallthrough
	case 7:
		fmt.Println("输出 switch穿透")
	default:
    	   fmt.Println("7")
	}
}

```

输出：

```
8
输出 switch穿透

```

**for循环**

for循环可以通过`break`、`goto`、`return`、`panic`语句强制退出循环。

```
for 初始语句;条件表达式;结束语句{
    循环体语句
}
```

省略初始语句：

```
for ; i < 10; i++ {
		fmt.Println(i)
	}
```

省略初始语句与结束语句(类似于 while：

```
for i < 10 {
		fmt.Println(i)
		i++
	}
```

```go
package main

import "fmt"

func main() {
	var n0 int = 8
	for i := n0; i < 15; i++ {
		fmt.Println(" ", i)
	}
}
```

```
  8
  9
  10
  11
  12
  13
  14
```

**for range 键值循环**

Go语言中可以使用`for range`遍历数组、切片、字符串、map 及通道（channel）。 通过`for range`遍历的返回值有以下规律：

1. 数组、切片、字符串返回索引和值。
2. map返回键和值。
3. 通道（channel）只返回通道内的值。

```
func main() {
	s := []int{1, 3, 5}

	for i := 0; i < len(s); i++ {
		fmt.Println(i, s[i])
	}

	for index, value := range s {
		fmt.Println(index, value)
	}
}
```



**goto（跳转到指定的标签**

`goto`语句通过标签进行代码间的无条件跳转。

```go
func main() {
	for i := 0; i < 3; i++ {
		for j := 0; j < 3; j++ {
			if (i == 2) && (j == 2) {
				goto breakTag//跳转到标签处
			}
			fmt.Println(i, j)
		}
	}

breakTag://标签
	fmt.Println("到这里结束")
}

```

```
0 0
0 1
0 2
1 0
1 1
1 2
2 0
2 1
到这里结束
```

**break 跳出循环**

`break`语句可以结束`for`、`switch`和`select`的代码块。

`break`语句还可以在语句后面添加标签，表示退出某个标签对应的代码块，标签要求必须定义在对应的`for`、`switch`和 `select`的代码块上。 举个例子：







### Array 数组

```
定义：
var 数组变量名 [元素数量]T
```

数组的长度必须是常量，并且长度是数组类型的一部分。一旦定义，长度不能变。 `[5]int`和`[10]int`是不同的类型。

数组可以通过下标进行访问，下标是从`0`开始，最后一个元素下标是：`len-1`。

数组是同一种数据类型元素的集合。 在Go语言中，数组从声明时就确定，使用时可以修改数组成员，但是数组大小不可变化。 基本语法：

```
// 定义一个长度为3元素类型为int的数组a
var a [3]int
```

**初始化方式**

1.使用初始化列表来设置数组元素的值。

```go
func main() { // main函数，是程序执行的入口
	var arr_1 [5]int
	var arr_2 = [5]int{1, 2}
	var arr_3 = [5]string{"一个", "两个", "三个"}
	fmt.Println(arr_1) //[0 0 0 0 0] 
	fmt.Println(arr_2) //[1 2 0 0 0]
	fmt.Println(arr_3) //[一个 两个 三个  ]
}
```

2.编译器根据初始值的个数自行推断数组的长度

```
func main() { // main函数，是程序执行的入口
	var testArray [3]int
	var numArray = [...]int{1, 2}
	var cityArray = [...]string{"北京", "上海", "深圳"}
	fmt.Println(testArray)                          //[0 0 0]
	fmt.Println(numArray)                           //[1 2]
	fmt.Printf("type of numArray:%T\n", numArray)   //type of numArray:[2]int
	fmt.Println(cityArray)                          //[北京 上海 深圳]
	fmt.Printf("type of cityArray:%T\n", cityArray) //type of cityArray:[3]string
}
```

3.使用指定索引值的方式来初始化数组:

```go
func main() { // main函数，是程序执行的入口
	a := [...]int{0: 1, 3: 5} //下标0 指定 1， 下标3 指定 5 ， 长度为最后一个指定下标
	fmt.Println(a) //[1 0 0 5]
	fmt.Printf("typr: %T\n", a) //typr: [4]int
}
```

**数组遍历**

```go
func main() {
	a := [...]int{0: 1, 3: 5}
	//for 循环遍历
	for i := 0; i < len(a); i++ {
		fmt.Println(a[i])
	}
	//2.使用 range 遍历数组
	for index, value := range a {
		fmt.Println(index, value)
	}
}
```

```
0
0
5
0 1
1 0
2 0
3 5
```

**多维数组**

二维数组：

多维数组只有第一层才能让编译器推断长度。

数组是**值类型**，赋值和传参会复制整个数组。因此改变副本的值，不会改变本身的值。

`[n]*T`表示**指针数组**，`*[n]T`表示**数组指针** 

```go
func main() {
	a := [3][3]string{
		{"我", "是", "go"},
		{"语", "言", "学"},
		{"习", "者", "啊"},
	}
	fmt.Println("a: ", a) //a:  [[我 是 go] [语 言 学] [习 者 啊]]
	fmt.Println("a[2]: ", a[2]) //a[2]:  [习 者 啊]
	fmt.Println("a[2][1] :", a[2][1]) //a[2][1] : 者
}
```



```
func main() {
	a := [3][3]string{
		{"我", "是", "go"},
		{"语", "言", "学"},
		{"习", "者", "啊"},
	}

	for _, v1 := range a { //省略位置为 index 下标值
		for _, v2 := range v1 {
			fmt.Println(v2)
		}
	}
}
```

```
我
是
go
语
言
学
习
者
啊
```

### !切片Slice

切片（Slice）是一个**拥有相同类型元素的可变长度的序列**。它是**基于数组类型**做的一层封装。它非常灵活，支持自动扩容。

**切片的本质**就是对底层数组的封装，它包含了三个信息：底层数组的指针、切片的长度（len）和切片的容量（cap）。

**切片是一个引用类型**，它的内部结构包含`地址`、`长度`和`容量`。切片一般用于快速地操作一块数据集合。

切片的遍历方式和数组是一致的，支持索引遍历和`for range`遍历。

声明切片类型的基本语法如下：

```go
var name []T
```



切片是一个引用类型，因此当 直接将一个切片赋值给另一个切片，实际上是两切片都指向同一片地址空间（底层的数组），因此如果利用索引改变其中一个切片的值，另一个切片也会改变。

测试：

```go
func main() {
	a := [5]int{1, 3, 5, 7, 8} 
       // var b []int 声明一个切片
	b := a[1:3] // b := a[low:high] 所的得到的是一个左闭右开区间，切片需要初始化才能使用
	fmt.Println(a) //[1 3 5 7 8]
	fmt.Println(b) //[3 5]
}
```

省略了`low`则默认为0；省略了`high`则默认为切片操作数的长度：

```go
a[2:]  // 等同于 a[2:len(a)]
a[:3]  // 等同于 a[0:3]
a[:]   // 等同于 a[0:len(a)]
```

**使用make()函数动态构造切片：**

```bash
make([]T, size, cap)
```

- T:切片的元素类型
- size:切片中元素的数量
- cap:切片的容量

```
func main() {
	a := make([]int, 10, 10)
	fmt.Println(a) //[0 0 0 0 0 0 0 0 0 0]
	fmt.Println(len(a)) //10
	fmt.Println(cap(a)) //10
}
```

**append()方法添加元素**

Go语言的内建函数`append()`可以为切片动态添加元素。 可以一次添加一个元素，可以添加多个元素，也可以添加另一个切片中的元素（后面加 `…` ）。

通过var声明的零值切片可以在`append()`函数直接使用，无需初始化。

**每个切片会指向一个底层数组**，这个数组的容量够用就添加新增元素。当底层数组不能容纳新增的元素时，切片就会**自动按照一定的策略进行“扩容”**，此时该切片指向的底层数组就会更换。“扩容”操作往往发生在`append()`函数调用时，所以我们通常都需要**用原变量接收append函数的返回值**。

```go
var s[]int
s = append(s, 1) //添加元素1
s = append(s, 1, 2, 3, 4) // 添加元素 1, 2, 3, 4
s2 := []int{5, 6, 7}
s = append(s, s2...) //添加切片s2的所有元素
```

**copy()函数**

Go语言内建的`copy()`函数可以迅速地将一个切片的数据复制到另外一个切片空间中，`copy()`函数的使用格式如下：

```bash
copy(destSlice, srcSlice []T)
```

其中：

- srcSlice: 数据来源切片
- destSlice: 目标切片

```go
	a := []int{1, 2, 3, 4, 5}
	c := make([]int, 5, 5)
	copy(c, a)     //使用copy()函数将切片a中的元素复制到切片c
```

**删除元素：**

从切片a中删除索引为`index`的元素，操作方法是`a = append(a[:index], a[index+1:]...)`

```
func main() {
	// 从切片中删除元素
	a := []int{30, 31, 32, 33, 34, 35, 36, 37}
	// 要删除索引为2的元素
	a = append(a[:2], a[3:]...)
	fmt.Println(a) //[30 31 33 34 35 36 37]
}
```

### map

Go语言中提供的映射关系容器为`map`，其内部使用`散列表（hash）`实现。

map是一种无序的基于`key-value`的数据结构，Go语言中的map是引用类型，必须初始化才能使用。(仅仅声明map 默认为 nil )

```go
map[KeyType]ValueType
```

- KeyType:表示键的类型。
- ValueType:表示键对应的值的类型。

map类型的变量默认初始值为nil，**需要使用make()函数来分配内存。**语法为：

```go
make(map[KeyType]ValueType, [cap])
```

其中cap表示map的容量，该参数虽然不是必须的，但是我们应该在初始化map的时候就为其指定一个合适的容量。

通过 key 获取值将有**两个返回值**，**第一个返回值是获取的值**，如果 key 不存在，返回空值，第二个参数是一个 bool 值，表示获取值是否获取成功。

**遍历**map时的元素顺序与添加键值对的顺序无关。

```go
func main() {
	//make 函数初始化
       scoreMap := make(map[string]int, 8)
	scoreMap["小明"] = 100
	scoreMap["小红"] = 99
	fmt.Println(scoreMap) //map[小明:100 小红:99]
	//声明时填充元素
	userMap := map[string]string{
		"小刚": "123",
		"小李": "321",
	}
	fmt.Println(userMap) //map[小刚:123 小李:321]

	//判断某个建是否存在
	//value, ok := map[key]
	value, ok := userMap["小李"]
	if ok {
		fmt.Println("找到了", value) //找到了 321
	} else {
		fmt.Println("未查到")
	}
    
    
    
    //遍历
	for k, v := range userMap {
		fmt.Println(k, v)
	}
	/*
		小刚 123
		小李 321
	*/
	//只遍历键
	for k := range userMap {
		fmt.Println(k)
	}
	/*
		小刚
		小李
	*/
}
```

**delete()**删除键值对

```go
func main() {
	myMap := make(map[string]int, 8)
	myMap["小李"] = 1
	myMap["小红"] = 2
	myMap["小宋"] = 3
	myMap["小王"] = 4

	//delete() 删除一组键值对 delete(mao, key)
	delete(myMap, "小李")

	for k := range myMap {
		fmt.Println(k)
	}
}
```

```
小红
小宋
小王
```

**指定序列遍历 map**

```go

import (
	"fmt" // 导入内置 fmt 包
	"math/rand"
	"sort"
)

func main() {
	testMap := make(map[string]int, 100)
	for i := 0; i < 50; i++ {
		//Sprintf根据format参数生成格式化的字符串并返回该字符串。
		key := fmt.Sprintf("stu%02d", i)
        	//返回一个取值范围在[0,n)的伪随机int值，如果n<=0会panic。
		value := rand.Intn(100) 
		testMap[key] = value
	}

	//取出map的key 放入切片
	var keySlice = make([]string, 0, 100)
	for key := range testMap {
		keySlice = append(keySlice, key)
	}
	//排序
	sort.Strings(keySlice)
	//按照排序后的顺序遍历输出
	for _, key := range keySlice {
		fmt.Println(key, testMap[key])
	}

}

```

```
stu00 81
stu01 87
stu02 47
stu03 59
stu04 81
..............
```



```
var sliceMap := make(map[string][]int,8) //队sliceMap初始化
//其中值为整数数组
```







### 函数

Go语言中定义函数使用`func`关键字，具体格式如下：

```go
func 函数名(参数)(返回值){
    函数体
}
```

- 函数名：由字母、数字、下划线组成。但函数名的第一个字母不能是数字。在同一个包内，函数名也称不能重名（包的概念详见后文）。
- 参数：参数由参数变量和参数变量的类型组成，多个参数之间使用`,`分隔。
- 返回值：返回值由返回值变量和其变量类型组成，也可以只写返回值的类型，多个返回值必须用`()`包裹，并用`,`分隔。
- 函数体：实现指定功能的代码块。



函数的参数中如果**相邻变量的类型相同**，则可以省略类型，例如：

```go
func intSum(x, y int) int {
	return x + y
}
```

**可变参数**是指函数的参数数量不固定。Go语言中的可变参数通过在参数名后加`...`来标识，可变参数通常要作为函数的最后一个参数。

```go
func intSum2(x ...int) int {
	fmt.Println(x) //x是一个切片
	sum := 0
	for _, v := range x {
		sum = sum + v
	}
	return sum
}
```

调用上面的函数：

```go
ret1 := intSum2()
ret2 := intSum2(10)
ret3 := intSum2(10, 20)
ret4 := intSum2(10, 20, 30)
fmt.Println(ret1, ret2, ret3, ret4) //0 10 30 60
```



Go语言中通过`return`关键字向外输出**返回值。**

Go语言中函数支持**多返回值**，函数如果有多个返回值时必须用`()`将所有返回值包裹起来。

举个例子：

```go
func calc(x, y int) (int, int) {
	sum := x + y
	sub := x - y
	return sum, sub
}
```

函数定义时可以给**返回值命名**，并在函数体中直接使用这些变量，最后通过`return`关键字返回。

例如：

```go
func calc(x, y int) (sum, sub int) {
	sum = x + y
	sub = x - y
	return
}
```

当我们的一个函数返回值类型为slice时，nil可以看做是一个有效的slice，没必要显示返回一个长度为0的切片。

```go
func someFunc(x string) []int {
	if x == "" {
		return nil // 没必要返回[]int{}
	}
	...
}
```

**函数类型和变量**

**定义函数类型**

使用`type`关键字来定义一个函数类型

例如：

```go
type calculation func(int, int) int
```

上面语句定义了一中函数类型，这种函数接受两个int参数，并且返回一个int返回值，那么我们将所有的满足这些参数，返回值的函数都称为该函数（calculation），函数可以赋值给同类型的变量

声明**函数类型的变量**并且赋值

```
var c calculation 
c = add
```

**函数作为参数和返回值**

参数：

```go
func add(x, y int) int {
	return x + y
}
func calc(x, y int, op func(int, int) int) int {
	return op(x, y)
}
func main() {
	ret2 := calc(10, 20, add)
	fmt.Println(ret2) //30
}
```

返回值：

```go
func do(s string) (func(int, int) int, error) {
	switch s {
	case "+":
		return add, nil
	case "-":
		return sub, nil
	default:
		err := errors.New("无法识别的操作符")
		return nil, err
	}
}
```

#### 匿名函数和闭包

**匿名函数**定义格式：

```go
func(参数)(返回值){
    函数体
}
```

匿名函数因为没有函数名，所以没办法像普通函数那样调用。

所以匿名函数需要**保存到某个变量**或者作为立即执行函数:

```go
func main() {
	// 将匿名函数保存到变量
	add := func(x, y int) {
		fmt.Println(x + y)
	}
	add(10, 20) // 通过变量调用匿名函数

	//自执行函数：匿名函数定义完加()立即执行
	func(x, y int) {
		fmt.Println(x + y)
	}(10, 20)
}
```

匿名函数多用于实现回调函数和闭包。

**闭包：**

闭包指的是一个函数和与其相关的引用环境组合而成的实体。简单来说，`闭包=函数+引用环境`

```go
func adder() func(int) int {
	var x int
	return func(y int) int {
		x += y
		return x
	}
}
func main() {
	var f = adder()
	fmt.Println(f(10)) //10
	fmt.Println(f(20)) //30 10 + 20 -> 上一次调用的x依旧存在，
	fmt.Println(f(30)) //60

	f1 := adder()
	fmt.Println(f1(40)) //40
	fmt.Println(f1(50)) //90
}
```

f作为一个函数，在他的生命周期中（在本例中 f函数 的生命周期为main函数的结束），f函数 内部的参数也一直有效。此时  f  就是一个闭包。

#### defer

Go语言中的`defer`语句会将其后面跟随的语句进行延迟处理。在`defer`归属的函数即将返回时，将延迟处理的语句按`defer`定义的逆序进行执行，也就是说，先被`defer`的语句最后被执行，最后被`defer`的语句，最先被执行。

在Go语言的函数中`return`语句在底层并不是原子操作，它分为给返回值赋值和RET指令两步。而`defer`语句执行的时机就在返回值赋值操作后，RET指令执行前。

#### 内置函数

|    内置函数    |                             介绍                             |
| :------------: | :----------------------------------------------------------: |
|     close      |                     主要用来关闭channel                      |
|      len       |      用来求长度，比如string、array、slice、map、channel      |
|      new       | 用来分配内存，主要用来分配值类型，比如int、struct。返回的是指针 |
|      make      |   用来分配内存，主要用来分配引用类型，比如chan、map、slice   |
|     append     |                 用来追加元素到数组、slice中                  |
| panic和recover |                        用来做错误处理                        |

### 包与依赖管理

**引用包**

在项目目录实行`go get`命令手动下载包：

```
go get -u github.com/cocovs/learnGoModule //自动下载最新半版本包
```

```
go get -u github.com/q1mi/hello@v0.1.0//可在后追加 @x.x.x 指定版本
```

**发布包**

首先在自己的 github 账号下新建一个项目，并把它下载到本地。

```
git clone https://github.com/XXXX
```

在项目目录下终端输入以下命令进行初始化项目，创建`go.mod`文件。定义项目的引入路径为github.com/cocovs/learn_go-module（仓库路径

```
go mod init github.com/cocovs/learn_go-module
```

项目根目录下创建 `hello.go` 文件

```
package main

import "fmt"

func SayHello_zy() {
	fmt.Println("hello,这里是我的第一个包")
}

```

将该项目的代码 push 到仓库的远端分支，这样就对外发布了一个Go包。其他人就可以通过`github.com/cocovs/learn_go-module` 引入路径下载、使用这个包了

```
git add . // 将当前目录添加到暂存区
git commit -m " first push"  //将暂存区内容提交至本地库
git tag -a v0.1.0 -m "release version v0.1.0" //使用git tag为代码包打上标签
git push origin v0.1.0  //推送至远程仓库并发布，版本号为v0.1.0的版本
```

其中v0.1.0 分别是主版本号、次版本号、修订号

- 主版本号：发布了不兼容的版本迭代时递增。
- 次版本号：发布了功能性更新时递增。
- 修订号：发布了bug修复类更新时递增。





### json



我们可以在 Golang 的结构体定义中添加 `omitempty` 关键字，来表示这条信息如果没有提供，在序列化成 json 的时候就不要包含其默认值。

![image-20220523084619782](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220523084619782.png)

https://www.jianshu.com/p/a2ed0d23d1b0





json序列化：go语言中的数据 -> json数据格式

序列化：

构造一个结构体，结构体的字段与json一一对应。



**结构体标签：**

`Tag`是结构体的元信息，可以在运行的时候通过反射的机制读取出来。 `Tag`在结构体字段的后方定义，由一对**反引号**包裹起来，具体的格式如下：

```bash
`key1:"value1" key2:"value2"`
```

结构体tag由一个或多个键值对组成。键与值使用冒号分隔，值用双引号括起来。同一个结构体字段可以设置多个键值对tag，不同的键值对之间使用空格分隔。

**注意事项：** 为结构体编写`Tag`时，必须严格遵守键值对的规则。结构体标签的解析代码的容错能力很差，一旦格式写错，编译和运行时都不会提示任何错误，通过反射也无法正确取值。例如不要在key和value之间添加空格。





### 接口







## Gin 框架



#### 函数：

**Query:**

```go
func (c *Context) Query(key string) string {
	value, _ := c.GetQuery(key)
	return value
}
```

Query返回参数值，如果不存在的话则返回空字符串""









```
package main

import (
   "github.com/gin-gonic/gin"
)

//
func sayHello(c *gin.Context) {
   c.JSON(200, gin.H{
      "message": "hello golang!",
   })
}
func main() {
   // 1.创建默认路由引擎
   r := gin.Default()
   // 2.绑定路由规则，执行的函数
   // gin.Context，封装了request和response
   // GET：请求方式；/hello：请求的路径
   r.GET("/hello", sayHello)

   //启动服务
   r.Run(":8080")
}
```

### RESTful API

简单来说，REST的含义就是客户端与Web服务器之间进行交互的时候，使用HTTP协议中的4个请求方法代表不同的动作。

- `GET`用来获取资源
- `POST`用来新建资源
- `PUT`用来更新资源
- `DELETE`用来删除资源。

### gin 获取URI 路径参数

```go
func main() {
	r := gin.Default()

	r.GET("/:name/:age", func(c *gin.Context) {
		name := c.Param("name")
		age := c.Param("age")
		c.JSON(http.StatusOK, gin.H{
			"name": name,
			"age":  age,
		})
	})
	r.GET("/blog/:year/:month",func (c *gin.Context)  {
		year := c.Params("year")
		month := c.Params("month")
		c.JSON(http.StatusOK, gin.H{
			"year" : year,
			"month" : month,
		})
	})
	r.Run()
}

```

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220514165939531.png" alt="image-20220514165939531" style="zoom:80%;" />



### gin获取querystring参数

应用场景：搜索引擎进行搜索关键字

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220621235430563.png" alt="image-20220621235430563" style="zoom:67%;" />



格式：`?name=123&id=123`  。路由后加`？`，各键值对用`&`连接

#### Query函数

获取请求中携带的参数

函数原型：

`func (c *Context) Query(key string) (value string)`

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220622000941626.png" alt="image-20220622000941626" style="zoom:80%;" />



```go
func main() {
	r := gin.Default()
	r.GET("/index", func(c *gin.Context) {
		//获取querystring参数
		name := c.Query("name")
		id := c.Query("id")
		c.JSON(http.StatusOK, gin.H{
			"name": name,
			"id":   id,
		})
	})
	r.Run()
}
```



**DefaultQuery**

设置默认值，若没有查找到边添加默认值。

`func (c *Context) DefaultQuery(key string, defaultValue string) string`


```go
		name := c.DefaultQuery("name", "test")
		id := c.DefaultQuery("id", "0")
```



<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220622002031785.png" alt="image-20220622002031785" style="zoom:80%;" />



**GetQuery**

`func (c *Context) GetQuery(key string) (string, bool)`

搜索到参数还将返回布尔值，搜索到 true，未搜索到 false



#### gin.Context.Query

func (*gin.Context).Query(key string) string



<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151207002.png" alt="image-20220620151207002" style="zoom:67%;" />



<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151229726.png" alt="image-20220620151229726" style="zoom:67%;" />

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151245240.png" alt="image-20220620151245240" style="zoom:67%;" />

QueryCache存放在context结构体内，使用url.ParseQuery缓存参数查询结果

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151733390.png" alt="image-20220620151733390" style="zoom:67%;" />

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151404465.png" alt="image-20220620151404465" style="zoom:67%;" />





### 参数绑定

`ShouldBind`会按照下面的顺序解析请求中的数据完成绑定：

1. 如果是 `GET` 请求，只使用 `Form` 绑定引擎（`query`）。
2. 如果是 `POST` 请求，首先检查 `content-type` 是否为 `JSON` 或 `XML`，然后再使用 `Form`（`form-data`）

```
package main

import (
	"fmt"
	"net/http"

	"github.com/gin-gonic/gin"
)

type UserInfo struct {
	//大写 外部可访问
	//form 从c.Context 中取出对应的绑定参数
	Username string `form:"username" json:"username" `
	Password string `form:"password" json:"password" `
}

func main() {
	r := gin.Default()

	r.GET("/user", func(c *gin.Context) {
		// username := c.Query("username")
		// password := c.Query("password")
		// u := UserInfo{
		// 	username: username,
		// 	password: password,
		// }
		var u UserInfo //声明UserInfo 类型变量u

		//请求相关数据绑定
		//ShouldBind检查Content-Type以自动选择绑定引擎，
		//具体取决于“Content-Type”标头使用不同的绑定：

		//****************ShuldBind***********************************
		err := c.ShouldBind(&u) //使用指针 传递指针才能修改
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": err.Error(),
			})
		} else {
			//fmt.Println("%#v\n", u)
			c.JSON(http.StatusOK, gin.H{
				"status": "ok",
			})
		}
	})
	//******************json*************************************
	r.POST("/json", func(c *gin.Context) {
		var u UserInfo          //声明UserInfo 类型变量u
		err := c.ShouldBind(&u) //使用指针 传递指针才能修改

		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": err.Error(),
			})
		} else {
			fmt.Println("json: \n", u)
			c.JSON(http.StatusOK, gin.H{
				// "username": u.Username,
				// "password": u.Password,
				"status": "ok",
			})
		}
	})

	//***************form*************************************
	//post 新建资源
	r.POST("/form", func(c *gin.Context) {
		var u UserInfo          //声明UserInfo 类型变量u
		err := c.ShouldBind(&u) //使用指针 传递指针才能修改
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": err.Error(),
			})
		} else {
			//fmt.Println("%#v\n", u)
			c.JSON(http.StatusOK, gin.H{
				"status": "ok",
			})
		}
	})

	r.Run()
}

```



使用postman 发送post请求 **form-data 数据**

![image-20220514182734984](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220514182734984.png)





使用postman 发送post ， **json 格式数据**

![image-20220514183428969](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220514183428969.png)





### 文件上传

上传文件就是处理了对方的post请求





















### gin 请求重定向

![image-20220518105256446](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220518105256446.png)



### 路由和路由组

路由组：

`func (group *RouterGroup) Group(relativePath string, handlers ...HandlerFunc) *RouterGroup`



```go

func main() {
	r := gin.Default()

	//访问index的GET请求会由以下匿名函数处理
	r.GET("/index", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "GET",
		})
	})
	//访问index的post请求会由以下匿名函数处理
	r.POST("/index", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "post",
		})
	})
	//访问index的put请求会由以下匿名函数处理
	r.PUT("/index", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "put",
		})
	})
	//访问index的delete请求会由以下匿名函数处理
	r.DELETE("/index", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "delete",
		})
	})
	//使用any处理所有的请求
	r.Any("/user", func(c *gin.Context) {
		switch c.Request.Method {
		case "GET":
			c.JSON(http.StatusOK, gin.H{"method": "GET"})
		case http.MethodPost:
			c.JSON(http.StatusOK, gin.H{"method": "POST"})
			//...
		}
	})
	//NoRoute 访问没有处理的定义的路由由以下逻辑进行处理
	r.NoRoute(func(c *gin.Context) {
		c.JSON(http.StatusNotFound, gin.H{})
	})

	//路由的组 路由组
	userGroup := r.Group("/video")
	userGroup.GET("/index", func(c *gin.Context) {c.JSON(http.StatusOK, gin.H{"method": "GET"})})
	userGroup.GET("/login", func(c *gin.Context) {c.JSON(http.StatusOK, gin.H{"method": "GET"})})
	userGroup.POST("/login", func(c *gin.Context) {c.JSON(http.StatusOK, gin.H{"method": "POST"})})
	
	
	_ = r.Run()
}
```



### Gin中间件

中间件适合处理公共的业务逻辑，例如登录校验，权限校验。

允许开发者在处理请求的过程中，加入用户自己的钩子（HOOK）函数 

Gin中的中间件必须是一个`gin.HandlerFunc`（包含请求上下文的函数）类型。

 **gin默认中间件**

`gin.Default()`默认使用了`Logger`和`Recovery`中间件，其中：

- `Logger`中间件将日志写入`gin.DefaultWriter`，即使配置了`GIN_MODE=release`。
- `Recovery`中间件会recover任何`panic`。如果有panic的话，会写入500响应码。

如果不想使用上面两个默认的中间件，可以使用`gin.New()`新建一个没有任何默认中间件的路由。

#### 注册中间件

1. **全局路由注册**

`func (engine *Engine) Use(middleware ...HandlerFunc) IRoutes`

全局路由注册后，所有路由都将先依次执行全局中间件函数

2. **单独注册中间件**

在GET函数中注册：

GET函数可以接收不定数量的参数，第二参数是` handlers ...HandlerFunc`,

```
r.GET("/index", m1Handler, m2Handler, indexHandler)
```

中间件将顺序依次执行。

3. **为路由组注册中间件**

   ​	第一种写法：

```go
	cocoGroup := r.Group("/coco")
	{
		cocoGroup.GET("/index", m2Handler, indexHandler)
		cocoGroup.GET("/hello", indexHandler)
	}
```

第二种写法：

```go
	cocoGroup.Use(m1Handler)
	{
		cocoGroup.GET("/index", m2Handler, indexHandler)
		cocoGroup.GET("/hello", indexHandler)
	}
```







### 小项目——任务清单

/#/ vue默认使用hash模式

![image-20220521174128393](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220521174128393.png)



可以在进入之前创造一个用户登陆注册页面，根据不同的用户返回不同的表单





![image-20220521172936946](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220521172936946.png)

添加任务时，post请求指向 /v1/todo

![image-20220521173003350](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220521173003350.png)







## GORM

默认情况下，GORM 会使用 `ID` 作为表的主键。

你可以通过标签 `primaryKey` 将其它字段设为主键

```go
// 将 `UUID` 设为主键
type Animal struct { 
ID   int64 
UUID  string `gorm:"primaryKey"` 
Name  string 
Age   int64
}
```

根据约定，数据表的列名使用的是 struct 字段名的 `蛇形命名`

```
type User struct {
  ID        uint      // 列名是 `id`
  Name      string    // 列名是 `name`
  Birthday  time.Time // 列名是 `birthday`
  CreatedAt time.Time // 列名是 `created_at`
}
```

您可以使用 `column` 标签或 [`命名策略`](https://gorm.io/zh_CN/docs/conventions.html#naming_strategy) 来覆盖列名

```
type Animal struct {
  AnimalID int64     `gorm:"column:beast_id"`         // 将列名设为 `beast_id`
  Birthday time.Time `gorm:"column:day_of_the_beast"` // 将列名设为 `day_of_the_beast`
  Age      int64     `gorm:"column:age_of_the_beast"` // 将列名设为 `age_of_the_beast`
}
```





ORM:提高开发效率

缺点：牺牲执行性能，灵活性，弱化SQL能力



* 数据表<——>结构体

* 数据行<——> 结构体实例

* 字段 <——>结构体字段

创建一个空数据库：

![image-20220519113004733](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220519113004733.png)



```go
package main

import (
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

type UserInfo struct {
	ID     uint
	Name   string
	Gender string
	Hobby  string
}

func main() {
	//连接MySQL  utf8mb4 较全的utf8编码  parseTime=True解析时间  local=Local 支持本地时间
	db, err := gorm.Open("mysql", "root:123456@(101.43.172.78:3306)/go_sql_db1?charset=utf8mb4&parseTime=True&loc=Local")
	if err != nil {
		panic(err)
	}
	//最后关闭表
	defer db.Close()
	//创建表 自动迁移 （把结构体和数据表进行对应）
	db.AutoMigrate(&UserInfo{})

	//创建数据行
	u1 := UserInfo{1, "coco", "man", "apex"}
	db.Create(&u1) //传入指针 快速
	
	//查询
	var u UserInfo
	//查询到的第一个存储在u中
	db.First(&u)
	fmt.Printf("u: %#v\n ", u)

	//更新
	db.Model(&u).Update("hobby", "music")

	//删除
	db.Delete(&u)
}

```

创建成功：

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220519113536379.png" alt="image-20220519113536379" style="zoom:67%;" />



**表名默认为结构体的复数**

使用 Tablename 改变表名：

```
type UserInfo struct {
	ID     uint
	Name   string
	Gender string
	Hobby  string
}

func (UserInfo) TableName() string {
	return "UserInfo"
}
```

或者在连接数据库后加上`db.SingularTable(true)`表示禁用表名复数。



**tag 定义默认值**

在创建表后使用tag便会将该表默认值改为该字段，之后修改tag，表默认值也不会变化，除非创建新表。

![image-20220520110002057](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220520110002057.png)

#### 查询

```go
// 根据主键查询第一条记录
db.First(&user)
//// SELECT * FROM users ORDER BY id LIMIT 1;

// 随机获取一条记录
db.Take(&user)
//// SELECT * FROM users LIMIT 1;

// 根据主键查询最后一条记录
db.Last(&user)
//// SELECT * FROM users ORDER BY id DESC LIMIT 1;

// 查询所有的记录
db.Find(&users)
//// SELECT * FROM users;

// 查询指定的某条记录(仅当主键为整型时可用)
db.First(&user, 10)
//// SELECT * FROM users WHERE id = 10;
```

**普通SQL查询**

```go
// Get first matched record
db.Where("name = ?", "jinzhu").First(&user)
//// SELECT * FROM users WHERE name = 'jinzhu' limit 1;

// Get all matched records
db.Where("name = ?", "jinzhu").Find(&users)
//// SELECT * FROM users WHERE name = 'jinzhu';

// <>
db.Where("name <> ?", "jinzhu").Find(&users)
//// SELECT * FROM users WHERE name <> 'jinzhu';

// IN
db.Where("name IN (?)", []string{"jinzhu", "jinzhu 2"}).Find(&users)
//// SELECT * FROM users WHERE name in ('jinzhu','jinzhu 2');

// LIKE
db.Where("name LIKE ?", "%jin%").Find(&users)
//// SELECT * FROM users WHERE name LIKE '%jin%';

// AND
db.Where("name = ? AND age >= ?", "jinzhu", "22").Find(&users)
//// SELECT * FROM users WHERE name = 'jinzhu' AND age >= 22;

// Time
db.Where("updated_at > ?", lastWeek).Find(&users)
//// SELECT * FROM users WHERE updated_at > '2000-01-01 00:00:00';

// BETWEEN
db.Where("created_at BETWEEN ? AND ?", lastWeek, today).Find(&users)
//// SELECT * FROM users WHERE created_at BETWEEN '2000-01-01 00:00:00' AND '2000-01-08 00:00:00';
```

### count

用于获取匹配的记录数

```go
var count int64
db.Model(&User{}).Where("name = ?", "jinzhu").Or("name = ?", "jinzhu 2").Count(&count)
// SELECT count(1) FROM users WHERE name = 'jinzhu' OR name = 'jinzhu 2'

db.Model(&User{}).Where("name = ?", "jinzhu").Count(&count)
// SELECT count(1) FROM users WHERE name = 'jinzhu'; (count)

db.Table("deleted_users").Count(&count)
// SELECT count(1) FROM deleted_users;

// Count with Distinct
db.Model(&User{}).Distinct("name").Count(&count)
// SELECT COUNT(DISTINCT(`name`)) FROM `users`

db.Table("deleted_users").Select("count(distinct(name))").Count(&count)
// SELECT count(distinct(name)) FROM deleted_users

// Count with Group
users := []User{
  {Name: "name1"},
  {Name: "name2"},
  {Name: "name3"},
  {Name: "name3"},
}

db.Model(&User{}).Group("name").Count(&count)
count // => 3
```

### 更新

`Save()`默认会更新该对象的所有字段（无论是否赋值）

Update或者Updates:更新指定字段

```go
// 更新单个属性，如果它有变化
db.Model(&user).Update("name", "hello")
//// UPDATE users SET name='hello', updated_at='2013-11-17 21:34:10' WHERE id=111;

// 根据给定的条件更新单个属性
db.Model(&user).Where("active = ?", true).Update("name", "hello")
//// UPDATE users SET name='hello', updated_at='2013-11-17 21:34:10' WHERE id=111 AND active=true;

// 使用 map 更新多个属性，只会更新其中有变化的属性
db.Model(&user).Updates(map[string]interface{}{"name": "hello", "age": 18, "active": false})
//// UPDATE users SET name='hello', age=18, active=false, updated_at='2013-11-17 21:34:10' WHERE id=111;

// 使用 struct 更新多个属性，只会更新其中有变化且为非零值的字段
db.Model(&user).Updates(User{Name: "hello", Age: 18})
//// UPDATE users SET name='hello', age=18, updated_at = '2013-11-17 21:34:10' WHERE id = 111;

// 警告：当使用 struct 更新时，GORM只会更新那些非零值的字段
// 对于下面的操作，不会发生任何更新，"", 0, false 都是其类型的零值
db.Model(&user).Updates(User{Name: "", Age: 0, Active: false})
```





























## Go使用SQL

### sql.DB

正如上文所言，sql.DB是数据库的抽象，虽然通常它容易被误以为是数据库连接。它提供了一些跟数据库交互的函数，同时管理维护一个数据库连接池，帮你处理了单调而重复的管理工作，并且在多个goroutines也是十分安全。

sql.DB表示是数据库抽象，因此你有几个数据库就需要为每一个数据库创建一个sql.DB对象。因为它维护了一个连接池，因此不需要频繁的创建和销毁。它需要长时间保持，因此最好是设置成一个全局变量以便其他代码可以访问。

创建数据库对象需要引入标准库database/sql，同时还需要引入驱动go-sql-driver/mysql。使用`_`表示引入驱动的变量，这样做的目的是为了在你的代码中不至于和标注库的函数变量namespace冲突。


链接：https://www.jianshu.com/p/340eb943be2e

### 连接池

只用sql.Open函数创建连接池，可是此时只是初始化了连接池，并没有创建任何连接。连接创建都是惰性的，只有当你真正使用到连接的时候，连接池才会创建连接。连接池很重要，它直接影响着你的程序行为。

连接池的工作原来却相当简单。当你的函数(例如Exec，Query)调用需要访问底层数据库的时候，函数首先会向连接池请求一个连接。如果连接池有空闲的连接，则返回给函数。否则连接池将会创建一个新的连接给函数。一旦连接给了函数，连接则归属于函数。函数执行完毕后，要不把连接所属权归还给连接池，要么传递给下一个需要连接的（Rows）对象，最后使用完连接的对象也会把连接释放回到连接池。

请求一个连接的函数有好几种，执行完毕处理连接的方式稍有差别，大致如下：

- db.Ping() 调用完毕后会马上把连接返回给连接池。
- db.Exec() 调用完毕后会马上把连接返回给连接池，但是它返回的Result对象还保留这连接的引用，当后面的代码需要处理结果集的时候连接将会被重用。
- db.Query() 调用完毕后会将连接传递给sql.Rows类型，当然后者迭代完毕或者显示的调用.Clonse()方法后，连接将会被释放回到连接池。
- db.QueryRow()调用完毕后会将连接传递给sql.Row类型，当.Scan()方法调用之后把连接释放回到连接池。
- db.Begin() 调用完毕后将连接传递给sql.Tx类型对象，当.Commit()或.Rollback()方法调用后释放连接。

因为每一个连接都是惰性创建的，如何验证sql.Open调用之后，sql.DB对象可用呢？通常使用db.Ping()方法初始化,调用了Ping之后，连接池一定会初始化一个数据库连接。



下载依赖：

```bash
go get -u github.com/go-sql-driver/mysql
```



`func Open(driverName, dataSourceName string) (*DB, error)`

Open打开一个dirverName指定的数据库，dataSourceName指定数据源，一般至少包括数据库文件名和其它连接必要的信息。



**sql.Open**并不会立即建立一个数据库的网络连接, 也不会对数据库链接参数的合法性做检验, 它仅仅是初始化一个sql.DB对象. 当真正进行第一次数据库查询操作时, 此时才会真正建立网络连接;

* sql.Open返回的sql.DB对象是协程并发安全的.

**sql.DB**表示操作数据库的抽象接口的对象，但不是所谓的数据库连接对象，sql.DB对象只有当需要使用时才会创建连接，如果想立即验证连接，需要用Ping()方法;

* sql.DB的设计就是用来作为长连接使用的。不要频繁Open, Close。比较好的做法是，为每个不同的datastore建一个DB对象，保持这些对象Open。如果需要短连接，那么把DB作为参数传入function，而不要在function中Open, Close。

* `sql.DB`是表示连接的数据库对象（结构体实例），它保存了连接数据库相关的所有信息。它内部维护着一个具有零到多个底层连接的连接池，它可以安全地被多个goroutine同时使用。





通常来说, 不应该直接使用驱动所提供的方法, 而是应该使用 sql.DB, 因此在导入 mysql 驱动时, 这里使用了匿名导入的方式(在包路径前添加 _), 当导入了一个数据库驱动后, 此驱动会自行初始化并注册自己到Golang的database/sql上下文中, 因此我们就可以通过 database/sql 包提供的方法访问数据库了.

```
import (
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
	"fmt"
)
```





## 雪花算法

![image-20220619220351999](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220619220351999.png)







```go
package main

//sf 为导入的包起一个别名
import (
	"fmt"
	sf "github.com/bwmarrin/snowflake"
	"time"
)

var (
	node *sf.Node
)

// Init 输入开始时间和机器码 初始化结点
func Init(startTime string, machineID int64) (err error) {
	var st time.Time
	st, err = time.Parse("2006-01-02", startTime)
	if err != nil {
		return
	}
	sf.Epoch = st.UnixNano() / 1000000
	node, err = sf.NewNode(machineID)
	return
}

//返回一个id
func GenID() int64 {
	return node.Generate().Int64()
}

func main() {
	err := Init("2002-06-01", 1)
	if err != nil {
		println("init err", err)
		return
	}
	for i := 0; i < 100; i++ {
		fmt.Println(GenID())
	}

}

-----------OUT---------
2654003688955187208
2654003688955187209
2654003688955187210
2654003688955187211
........................................

```








## 并发编程
### goroutine

使用goroutine，在函数或者方法调用前加上go关键字就可以创建一个goroutine，从而令函数或者方法在该goroutine中运行。

#### goroutine调度

操作系统内核在调度时会挂起当前正在执行的线程并将寄存器中的内容保存到内存中，然后选出接下来要执行的线程并从内存中恢复该线程的寄存器信息，然后恢复执行该线程的现场并开始执行线程。从一个线程切换到另一个线程需要完整的上下文切换。因为可能需要多次内存访问，索引这个切换上下文的操作开销较大，会增加运行的cpu周期。

区别于操作系统内核调度操作系统线程，goroutine 的调度是Go语言运行时（runtime）层面的实现，是完全由 Go 语言本身实现的一套调度系统——go scheduler。它的作用是按照一定的规则将所有的 goroutine 调度到操作系统线程上执行。

GPM模型：

其中：

- G：表示 goroutine，每执行一次`go f()`就创建一个 G，包含要执行的函数和上下文信息。
- 全局队列（Global Queue）：存放等待运行的 G。
- P：表示 goroutine 执行所需的资源，最多有 GOMAXPROCS 个。
- P 的本地队列：同全局队列类似，存放的也是等待运行的G，存的数量有限，不超过256个。新建 G 时，G 优先加入到 P 的本地队列，如果本地队列满了会批量移动部分 G 到全局队列。
- M：线程想运行任务就得获取 P，从 P 的本地队列获取 G，当 P 的本地队列为空时，M 也会尝试从全局队列或其他 P 的本地队列获取 G。M 运行 G，G 执行之后，M 会从 P 获取下一个 G，不断重复下去。
- Goroutine 调度器和操作系统调度器是通过 M 结合起来的，每个 M 都代表了1个内核线程，操作系统调度器负责把内核线程分配到 CPU 的核上执行。

![gpm](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/gpm.png)





单从线程调度讲，Go语言相比起其他语言的优势在于OS线程是由OS内核来调度的， goroutine 则是由Go运行时（runtime）自己的调度器调度的，完全是在用户态下完成的， 不涉及内核态与用户态之间的频繁切换，包括内存的分配与释放，都是在用户态维护着一块大的内存池， 不直接调用系统的malloc函数（除非内存池需要改变），成本比调度OS线程低很多。 另一方面充分利用了多核的硬件资源，近似的把若干goroutine均分在物理线程上， 再加上本身 goroutine 的超轻量级，以上种种特性保证了 goroutine 调度方面的性能。

**动态栈：**

os线程（操作系统线程）一般有固定的栈内存（大约2mb），而一个goroutine 的栈在其生命周期开始只有很小的栈（通常为2kb），goroutinee的栈不固定，按需增大和缩小，最大限制为1GB，因此理论上goroutine可以创建十万次goroutine。Go 的 runtime （运行时） 会自动为 goroutine 分配合适的栈空间。



#### 等待组sync.WaitGroup

使用`time.Sleep(time.Second)`或者**sync.WaitGroup** 等待组 保证goroutine运行结束后 main再退出，否则main退出后，其他的goroutine也会结束。因为**main便是由一个主goroutine**开启的，**主goroutine结束后子goroutine也会结束**

后台各个子goroutine执行顺序是不固定的。

每个 sync.WaitGroup 值在内部维护着一个计数，此计数的初始默认值为零。

* 当一个协程调用了 wg.Wait() 时
  - 如果此时 wg 维护的计数为零，则此 wg.Wait() 此操作为一个空操作（noop）；
  - 否则（计数为一个正整数），此协程将进入**阻塞状态**。当以后其它某个协程将此计数更改至 0 时（一般通过调用 wg.Done()），此协程将重新进入运行状态（即 wg.Wait() 将返回）。

```go
package main

import (
	"fmt"
	"sync"
)

//计数器
var wg sync.WaitGroup

func hello(i int) {
	fmt.Println("this is hello", i)
	wg.Done() //通知wg把计数器-1 不减的话会进去死锁
}

func main() { //开启一个主goroutine去执行main函数 主goroutine结束后 子goroutine也会结束
	//开启100个goroutine
	for i := 0; i < 100; i++ {
		wg.Add(1)
		go hello(i) //go关键字开启一个goroutine 去执行这个函数
	}
	fmt.Println("this is main ")
	wg.Wait() //等待计数器清空
}

```





当使用匿名函数时出现输出结果很多相同的情况：

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220715001721288.png" alt="image-20220715001721288" style="zoom: 50%;" />

该情况原因是因为该匿名函数形成闭包，goroutine时内部函数会在外部寻找变量 i ，但goroutine的函数执行时间和外部 i 的变化不是同步的（当goroutine执行时，i 已经变化）因此会出现输出相同值的情况。



改正：

**显式**的给匿名函数传递参数，传递时将i复制一份传入匿名函数。

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220715002304869.png" alt="image-20220715002304869" style="zoom:50%;" />





#### GOMAXPROCS

操作系统线程和goroutine的关系：

* 一个操作系统线程对应用户态多个goroutine
* go程序可以同时使用多个操作系统线程
* goroutine和os线程是多对多关系 ,即n：m

go语言调度器使用GOMAXPROCS参数来确定需要使用多少个os线程来同时执行go代码。默认值是机器的CPU核心数。（1.5以后）

单核心下 多个goroutine 便只能并发执行。

（windows下运行可能有所不同）

```go
runtime.GOMAXPROCS(1)
```





### gochannel

channel是引用类型，需要使用 make 初始化并且根据缓冲区分为有、无缓冲区通道

当向无缓冲区通道发送数据将会阻塞。

```go
var ch1 chan int 
ch1 = make(chan int, 1) //有缓冲区通道 缓冲区设为 1
//ch1 = make(chan int) //无缓冲区通道
```

通道关闭后，仍然可以读取到数据，读完后也可以读到通道类型零值，因此只能通过通道返回值 ok 来进行通道是否关闭的判断。

**无缓冲区通道**

使用无缓冲通道进行通信将导致发送和接收的 goroutine 同步化。因此，无缓冲通道也被称为`同步通道`。



如果没有接受者，向无缓冲区通带发送数据将会一直阻塞，直到有接受者接收。

```go

func recv(c chan int) {
	n := <-c //接受数据
	fmt.Println("recv: ", n)
}

func main() {

	var ch1 chan int // channel是引用类型，需要初始化
	ch1 = make(chan int)
	go recv(ch1) //goroutine 调用函数
	ch1 <- 10  //发送
	fmt.Println("this is main")
}

//输出
recv:  10
this is main
```





**有缓冲区通道**

使用 make 函数初始化通道时，可以为其指定通道的容量，即有缓冲区通道

只要通道的容量大于零，那么该通道就属于有缓冲的通道，通道的容量表示通道中最大能存放的元素数量。当通道内已有元素数达到最大容量后，再向通道执行发送操作就会阻塞，除非有从通道执行接收操作。

我们可以使用内置的`len`函数获取通道内元素的数量，使用`cap`函数获取通道的容量，虽然我们很少会这么做。



**多返回值模式**

```go
value, ok := <- ch
```

其中：

- value：从通道中取出的值，如果通道被关闭则返回对应类型的零值。
- ok：通道ch关闭时返回 false，否则返回 true。

目前Go语言中并没有提供一个不对通道进行读取操作就能判断通道是否被关闭的方法。不能简单的通过`len(ch)`操作来判断通道是否被关闭。



**for range 接受值**

```go
func c3(ch chan int) {
	for i := range ch {
		fmt.Println("c3: ", i)
	}
}
```





**单向通道**

#### 消费生产者模型

```go
//接受通道 生产者
func producer() chan int {
   ch := make(chan int, 10)
   go func() {
      for i := 0; i < 10; i++ {
         ch <- i
      }
      close(ch)
   }()

   return ch
}

//发送通道 消费者 返回一个通道
func consumer(ch chan int) int {
   sum := 0
   for {
      v, ok := <-ch
      if !ok {
         break
      }
      sum += v
   }
   return sum
}

func main() {
   ch := producer()
   sum := consumer(ch)
   fmt.Println(sum)
}
```

在以下消费者函数producer中 其实也可以 向通道发送操作，但是们只希望消费者进行接受操作，而生产者进行发送操作。因此引入 **单向通道**来处理这种需要限制通道只能进行某种操作的情况。

```go
<- chan int // 只接收通道，只能接收不能发送
chan <- int // 只发送通道，只能发送不能接收
```

生产者返回一个只读通道，消费者接收一个只读通道

```go
//生产者 写入数据
//返回一个只能读取数据的通道 只读通道
func producer() <-chan int {
	ch := make(chan int, 10)
	go func() {
		for i := 0; i < 10; i++ {
			ch <- i
		}
		close(ch)
	}()

	return ch
}

//消费者 读取数据
//接收一个只读通道
func consumer(ch <-chan int) int {
	sum := 0
	for {
		v, ok := <-ch
		if !ok {
			break
		}
		sum += v
	}
	return sum
}
```

![img](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/channel.png)





#### select

select关键字类似switch语句，通过一系列case分支实现同时响应多通道的操作，每一个case对应一个通道的通信过程（发送或者接收），select会一直等待，直到某个case的通信操作完成时，就会执行某case下的操作。

例如：

```
for {
		select {
		case <-ch1:
			fmt.Println("ch1")
		case <-ch2:
			fmt.Println("ch2")
		}
		time.Sleep(time.Second)
	}
```



当多个case都满足，那么selecr将会随机选择一个执行。

没有case的select会一直阻塞，防止main退出。

```go

func main() {

	ch1 := make(chan int, 1)
	ch2 := make(chan int, 1)

	go func() {
		for {
			ch1 <- 1
		}
	}()

	go func() {
		for {
			ch2 <- 2
		}
	}()

	for {
		select {
		case <-ch1:
			fmt.Println("ch1")
		case <-ch2:
			fmt.Println("ch2")
		}
		time.Sleep(time.Second)
	}

}

//输出
ch2
ch2
ch2
ch1
ch2
ch1
.......
```





### 并发安全和锁

当多个goroutine对临界区资源进行操作时，可能会发生 竞态问题 （数据竞态）。

例子：

输出结果每次运行都不同，因为多个goroutine获取 i ，可能同时对其进行多次操作，但结果相当于操作一次。

```go
var (
	x  int64
	wg sync.WaitGroup //等待组 计数器
)

func add() {
	for i := 1; i < 50000; i++ {
		x = x + 1
	}
	wg.Done()
}

func main() {
	wg.Add(2)
	go add()
	go add()
	wg.Wait()
	fmt.Println("x: ", x)
}

//输出结果
 66898
或
89039
.......
```



#### 互斥锁 mutex

func (m *Mutex) Lock() ： 获取互斥锁

func (m *Mutex) Unlock() ： 释放互斥锁









## gRPC

GRPC基于HTTP2

### 安装和准备工作

安装go专用的protoc的生成器:

```
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
```



将消息类型用在RPC系统中，在.proto文件中定义一个RPC服务接口，protocol buffer编译器会根据所选择的不同语言生成服务接口代码及存根

````protobuf
service SearchService {
 //rpc 服务的函数名 （传入参数） 返回 （返回参数）
 rpc Search (SearchRequest) returns (SearchResponse);
}
````

、

更新并安装protoc插件

```
go get -u google.golang.org/protobuf/cmd/protoc-gen-go
go get -u google.golang.org/grpc/cmd/protoc-gen-go-grpc
go install google.golang.org/protobuf/cmd/protoc-gen-go
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc
```



在proto文件夹中创建xxx.proto文件

使用protoc 将proto文件编译为 xxx.pb.go 和 xxx_grpc.pb.go 文件

```
protoc --go_out=. product.proto 
protoc --go-grpc_out=. product.proto 
```

一次生成：

```
protoc  --go_out=./service --go-grpc_out=./service pbfile\product.proto
```

--go_out=./service表示生成的GO代码的保存路径

 --go-grpc_out=./service       生成xxx_grpc.pb.go文件

pbfile\product.proto  proto目标文件





proto文件可以通过 import 来引用其他的proto包



### protocal buffers

gRPC 使用 [protocol buffers](https://developers.google.com/protocol-buffers)作为接口定义语言(IDL)来描述服务接口和有效负载消息的结构。

下面是一个完整的 proto 文件

```protobuf
syntax = "proto3"; // 版本声明，使用Protocol Buffers v3版本

option go_package = "hello_server/pb;pb";  // 指定go package名称

package pb; // 包名

// 定义服务
service Greeter {
  // SayHello 方法
  rpc SayHello (HelloRequest) returns (HelloResponse) {}
}

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```

#### 定义服务

定义一个 RPC 服务，其方法接受你的 `SearchRequest`并返回一个 `SearchResponse`



#### 分配字段编号

如你所见，消息定义中的每个字段都有一个**唯一的编号**。这些字段编号用来在消息二进制格式中标识字段，在消息类型使用后就不能再更改。注意，范围1到15中的字段编号需要一个字节进行编码，包括字段编号和字段类型。范围16到2047的字段编号采用两个字节。因此，应该为经常使用的消息元素保留数字1到15的编号。切记为将来可能添加的经常使用的元素留出一些编号。

你可以指定的最小字段数是1，最大的字段数是 229−1229−1 ，即536,870,911。你也不能使用19000到19999 的编号，它们是预留给Protocol Buffers协议实现的。如果你在你的`.proto`文件中使用了预留的编号Protocol Buffers编译器就会报错。同样，你也不能使用任何之前保留的字段编号。



### grpc定义四种类型服务方法

- 普通 rpc，客户端向服务器发送一个请求，然后得到一个响应，就像普通的函数调用一样。

```protobuf
  rpc SayHello(HelloRequest) returns (HelloResponse);
```

- 服务器流式 rpc，其中客户端向服务器发送请求，并获得一个流来读取一系列消息。客户端从返回的流中读取，直到没有更多的消息。gRPC 保证在单个 RPC 调用中的消息是有序的。

```protobuf
  rpc LotsOfReplies(HelloRequest) returns (stream HelloResponse);
```

- 客户端流式 rpc，其中客户端写入一系列消息并将其发送到服务器，同样使用提供的流。一旦客户端完成了消息的写入，它就等待服务器读取消息并返回响应。同样，gRPC 保证在单个 RPC 调用中对消息进行排序。

```protobuf
  rpc LotsOfGreetings(stream HelloRequest) returns (HelloResponse);
```

- 双向流式 rpc，其中双方使用读写流发送一系列消息。这两个流独立运行，因此客户端和服务器可以按照自己喜欢的顺序读写: 例如，服务器可以等待接收所有客户端消息后再写响应，或者可以交替读取消息然后写入消息，或者其他读写组合。每个流中的消息是有序的。













### 认证

> 客户端和服务端之间调用，我们可以通过加入证书的方式，实现调用的安全性

TLS（Transport Layer Security，安全传输层)，TLS是建立在`传输层`TCP协议之上的协议，服务于应用层，它的前身是SSL（Secure Socket Layer，安全套接字层），它实现了将应用层的报文进行加密后再交由TCP进行传输的功能。

TLS协议主要解决如下三个网络安全问题。

- 保密(message privacy)，保密通过加密encryption实现，所有信息都加密传输，第三方无法嗅探；
- 完整性(message integrity)，通过MAC校验机制，一旦被篡改，通信双方会立刻发现；
- 认证(mutual authentication)，双方认证,双方都可以配备证书，防止身份被冒充；

#### 1. 生成自签证书

> 生产环境可以购买证书或者使用一些平台发放的免费证书

* 安装openssl

  网站下载：http://slproweb.com/products/Win32OpenSSL.html

  （mac电脑 自行搜索安装）

* 生成私钥文件

  ~~~shell
  ## 需要输入密码
  openssl genrsa -des3 -out ca.key 2048
  ~~~

  密码123456

* 创建证书请求

  ~~~shell
  openssl req -new -key ca.key -out ca.csr
  ~~~

* 生成ca.crt   证书

  ~~~shell
  openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt
  ~~~

找到openssl.cnf 文件

cmd输入可查询openssl.cnf文件位置 openssl version -d

1. 打开copy_extensions = copy

2. 打开 req_extensions = v3_req

3. 找到[ v3_req ],添加 subjectAltName = @alt_names

4. 添加新的标签 [ alt_names ] , 和标签字段

   指明验证域名

   ~~~ini
   [ alt_names ]
   
   DNS.1 = *.mszlu.com
   ~~~

5. 生成证书私钥server.key

   ~~~shell
   openssl genpkey -algorithm RSA -out server.key
   ~~~

   

6. 通过私钥server.key生成证书请求文件server.csr

   ~~~shell
   openssl req -new -nodes -key server.key -out server.csr -days 3650 -config ./openssl.cnf -extensions v3_req
   ~~~

7. 生成SAN证书  通过CA来生成证书

   ~~~shell
   openssl x509 -req -days 365 -in server.csr -out server.pem -CA ca.crt -CAkey ca.key -CAcreateserial -extfile ./openssl.cnf -extensions v3_req
   ~~~

- **key：** 服务器上的私钥文件，用于对发送给客户端数据的加密，以及对从客户端接收到数据的解密。
- **csr：** 证书签名请求文件，用于提交给证书颁发机构（CA）对证书签名。
- **crt：** 由证书颁发机构（CA）签名后的证书，或者是开发者自签名的证书，包含证书持有人的信息，持有人的公钥，以及签署者的签名等信息。
- **pem：** 是基于Base64编码的证书格式，扩展名包括PEM、CRT和CER。

什么是 SAN？

SAN（Subject Alternative Name）是 SSL 标准 x509 中定义的一个扩展。使用了 SAN 字段的 SSL 证书，可以扩展此证书支持的域名，使得一个证书可以支持多个不同域名的解析。



#### 2. 服务端应用证书

将`server.key`和`server.pem` copy到程序中

~~~go
func main()  {

	//添加证书
	file, err2 := credentials.NewServerTLSFromFile("keys/mszlu.pem", "keys/mszlu.key")
	if err2 != nil {
		log.Fatal("证书生成错误",err2)
	}
	rpcServer := grpc.NewServer(grpc.Creds(file))

	service.RegisterProdServiceServer(rpcServer,service.ProductService)

	listener ,err := net.Listen("tcp",":8002")
	if err != nil {
		log.Fatal("启动监听出错",err)
	}
	err = rpcServer.Serve(listener)
	if err != nil {
		log.Fatal("启动服务出错",err)
	}
	fmt.Println("启动grpc服务端成功")
}

~~~

#### 3. 客户端认证

公钥copy到客户端

~~~go
func main()  {
	file, err2 := credentials.NewClientTLSFromFile("client/keys/mszlu.pem", "*.mszlu.com")
	if err2 != nil {
		log.Fatal("证书错误",err2)
	}
	conn, err := grpc.Dial(":8002", grpc.WithTransportCredentials(file))

	if err != nil {
		log.Fatal("服务端出错，连接不上",err)
	}
	defer conn.Close()

	prodClient := service.NewProdServiceClient(conn)

	request := &service.ProductRequest{
		ProdId: 123,
	}
	stockResponse, err := prodClient.GetProductStock(context.Background(), request)
	if err != nil {
		log.Fatal("查询库存出错",err)
	}
	fmt.Println("查询成功",stockResponse.ProdStock)
}

~~~

上述认证方式为单向认证：

![img](D:/1Resources/1_learn/cs book/grpc/02/img/1586953-20210625171059706-1447106002-16509094111532.png)

中间人攻击

#### 4. 双向认证

![img](D:/1Resources/1_learn/cs book/grpc/02/img/1586953-20210625211235069-195172761-16509094417774.png)

上面的server.pem和server.key 是服务端的 公钥和私钥。

如果双向认证，客户端也需要生成对应的公钥和私钥。

私钥：

~~~shell
openssl genpkey -algorithm RSA -out client.key
~~~

证书:

~~~shell
openssl req -new -nodes -key client.key -out client.csr -days 3650 -config ./openssl.cnf -extensions v3_req
~~~

SAN证书：

~~~shell
openssl x509 -req -days 365 -in client.csr -out client.pem -CA ca.crt -CAkey ca.key -CAcreateserial -extfile ./openssl.cnf -extensions v3_req
~~~

服务端：

~~~go
func main() {

	//添加证书
	//file, err2 := credentials.NewServerTLSFromFile("keys/mszlu.pem", "keys/mszlu.key")
	//if err2 != nil {
	//	log.Fatal("证书生成错误",err2)
	//}
	// 证书认证-双向认证
	// 从证书相关文件中读取和解析信息，得到证书公钥、密钥对
	cert, err := tls.LoadX509KeyPair("cert/server.pem", "cert/server.key")
	if err != nil {
		log.Fatal("证书读取错误", err)
	}
	// 创建一个新的、空的 CertPool
	certPool := x509.NewCertPool()
	//双向认证通过ca证书
	ca, err := ioutil.ReadFile("cert/ca.crt")
	if err != nil {
		log.Fatal("ca证书读取错误", err)
	}
	// 尝试解析所传入的 PEM 编码的证书。如果解析成功会将其加到 CertPool 中，便于后面的使用
	certPool.AppendCertsFromPEM(ca)
	// 构建基于 TLS 的 TransportCredentials 选项
	creds := credentials.NewTLS(&tls.Config{
		// 设置证书链，允许包含一个或多个
		Certificates: []tls.Certificate{cert},
		// 要求必须校验客户端的证书。可以根据实际情况选用以下参数
		ClientAuth: tls.RequireAndVerifyClientCert,
		// 设置根证书的集合，校验方式使用 ClientAuth 中设定的模式
		ClientCAs: certPool,
	})

	rpcServer := grpc.NewServer(grpc.Creds(creds))

	service.RegisterProdServiceServer(rpcServer, service.ProductServerice)

	listener, err := net.Listen("tcp", ":8002")
	if err != nil {
		log.Fatal("启动监听出错", err)
	}
	err = rpcServer.Serve(listener)
	if err != nil {
		log.Fatal("启动服务出错", err)
	}
	fmt.Println("启动grpc服务端成功")
}

~~~

客户端：

~~~go
func main() {
	//file, err2 := credentials.NewClientTLSFromFile("client/keys/mszlu.pem", "*.mszlu.com")
	//if err2 != nil {
	//	log.Fatal("证书错误",err2)
	//}
	// 证书认证-双向认证
	// 从证书相关文件中读取和解析信息，得到证书公钥、密钥对
	cert, _ := tls.LoadX509KeyPair("cert/server.pem", "cert/server.key")
	// 创建一个新的、空的 CertPool
	certPool := x509.NewCertPool()
	ca, _ := ioutil.ReadFile("cert/ca.crt")
	// 尝试解析所传入的 PEM 编码的证书。如果解析成功会将其加到 CertPool 中，便于后面的使用
	certPool.AppendCertsFromPEM(ca)
	// 构建基于 TLS 的 TransportCredentials 选项
	creds := credentials.NewTLS(&tls.Config{
		// 设置证书链，允许包含一个或多个
		Certificates: []tls.Certificate{cert},
		// 要求必须校验客户端的证书。可以根据实际情况选用以下参数
		ServerName: "*.mszlu.com",
		RootCAs:    certPool,
	})

	conn, err := grpc.Dial(":8002", grpc.WithTransportCredentials(creds))

	if err != nil {
		log.Fatal("服务端出错，连接不上", err)
	}
	defer conn.Close()

	prodClient := service.NewProdServiceClient(conn)

	request := &service.ProductRequest{
		ProdId: 123,
	}
	stockResponse, err := prodClient.GetProductStock(context.Background(), request)
	if err != nil {
		log.Fatal("查询库存出错", err)
	}
	fmt.Println("查询成功", stockResponse.ProdStock)
}

~~~

#### 5. Token认证

##### 5.1 服务端添加用户名密码的校验

~~~go
func main()  {
	var authInterceptor grpc.UnaryServerInterceptor
	authInterceptor = func(
		ctx context.Context,
		req interface{},
		info *grpc.UnaryServerInfo,
		handler grpc.UnaryHandler,
	) (resp interface{}, err error) {
		//拦截普通方法请求，验证 Token
		err = Auth(ctx)
		if err != nil {
			return
		}
		// 继续处理请求
		return handler(ctx, req)
	}
	server := grpc.NewServer(grpc.UnaryInterceptor(authInterceptor))
	service.RegisterProdServiceServer(server,service.ProductService)

	listener, err := net.Listen("tcp", ":8002")
	if err != nil {
		log.Fatal("服务监听端口失败", err)
	}
	err = server.Serve(listener)
	if err != nil {
		log.Fatal("服务、启动失败", err)
	}
	fmt.Println("启动成功")
}


func Auth(ctx context.Context) error {
	md, ok := metadata.FromIncomingContext(ctx)
	if !ok {
		return fmt.Errorf("missing credentials")
	}
	var user string
	var password string

	if val, ok := md["user"]; ok {
		user = val[0]
	}
	if val, ok := md["password"]; ok {
		password = val[0]
	}

	if user != "admin" || password != "admin" {
		return status.Errorf(codes.Unauthenticated, "token不合法")
	}
	return nil
}

~~~

##### 5.2 客户端实现





客户端需要实现 `PerRPCCredentials` 接口。

~~~go
type PerRPCCredentials interface {
	// GetRequestMetadata gets the current request metadata, refreshing
	// tokens if required. This should be called by the transport layer on
	// each request, and the data should be populated in headers or other
	// context. If a status code is returned, it will be used as the status
	// for the RPC. uri is the URI of the entry point for the request.
	// When supported by the underlying implementation, ctx can be used for
	// timeout and cancellation. Additionally, RequestInfo data will be
	// available via ctx to this call.
	// TODO(zhaoq): Define the set of the qualified keys instead of leaving
	// it as an arbitrary string.
	GetRequestMetadata(ctx context.Context, uri ...string) (map[string]string, error)
	// RequireTransportSecurity indicates whether the credentials requires
	// transport security.
	RequireTransportSecurity() bool
}
~~~

`GetRequestMetadata` 方法返回认证需要的必要信息，`RequireTransportSecurity` 方法表示是否启用安全链接，在生产环境中，一般都是启用的，但为了测试方便，暂时这里不启用了。

实现接口：

~~~go
type Authentication struct {
    User     string
    Password string
}

func (a *Authentication) GetRequestMetadata(context.Context, ...string) (
    map[string]string, error,
) {
    return map[string]string{"user": a.User, "password": a.Password}, nil
}

func (a *Authentication) RequireTransportSecurity() bool {
    return false
}
~~~

应用：

~~~go
user := &auth.Authentication{
		User: "admin",
		Password: "admin",
	}
	conn, err := grpc.Dial(":8002", grpc.WithTransportCredentials(insecure.NewCredentials()),grpc.WithPerRPCCredentials(user))
	
~~~

