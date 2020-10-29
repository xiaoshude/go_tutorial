# 程序的基本组成元素

理解一段程序的通用策略：

divide conquer combine

把一段 Go 代码拆分后有哪些组成部分呢？

和其他编程语言一样，每个程序都是由以下部分组成：

* 变量储存值 
* 简单表达式通过加减操作合并成大的
* 基本数据类型聚合成复杂数据类型
* 表达式通过 if for 控制执行顺序
* 语句组织成函数用于隔离和复用
* 函数被组织成文件和包

我们分类来看每一部分的组成元素。

## 名称

起名字是一个减轻思考负担的重要技巧。函数、变量、常量、类型等的名称命名规则和 js 没有不同：开头字母或下划线，而且不能是关键字。

go里 25个关键字。

另外还有30多个名称被内置常量、类型和函数占用。如果使用就会覆盖内置声明。后面我们会逐渐遇到。

下面说下不同于 js 的一些地方。

1、包的名字总是由小写字母组成。比如最常用的 fmt 包。 2、驼峰命名，这点和 js 一样。 3、首字母大写，指示声明包外可见（前提是，声明本身不在块级作用域内）。所以，我们使用的包的方法都是大写字母开头的。

## 声明

* 变量声明 var ，等价于 js 中 let
* 常量声明 const ，等价于 const
* 函数声明 func，等价于 function
* 声明类型 type，可以理解为 TS 中的 type，但是不等价。下面详细说下这里。

### 类型

一个变量分为两部分： 1. 值 2. 类型

类型的本质是一个特性集的名字。所以每个类型都携带丰富信息：

* 大小（多少位）
* 如何解释内存的 01 数据
* 能进行的合法操作
* 关联了哪些方法

JS 本身没有自定义类型的能力，TS 提供自定义类型和编译时校验。 这里对比下 TS 的类型和 Go 的类型。

1. ts 是鸭子类型，只要结构一致，就认为是相同的类型，可以互相赋值。

[下面的例子](https://www.typescriptlang.org/play?#code/PTAEHUFMBsGMHsC2lQBd5oBYoCoE8AHSAZVgCcBLA1UABWgEM8BzM+AVwDsATAGiwoBnUENANQAd0gAjQRVSQAUCEmYKsTKGYUAbpGF4OY0BoadYKdJMoL+gzAzIoz3UNEiPOofEVKVqAHSKymAAmkYI7NCuqGqcANag8ABmIjQUXrFOKBJMggBcISGgoAC0oACCoASMFmgY7p7ehCTkVOle4jUMdRLYTqCc8LEZzCZmoNJODPHFZZXVtZYYkAAeRJTInDQS8po+rf40gnjbDKv8LqD2jpbYoACqAEoAMsK7sUmxkGSCc+VVQQuaTwVb1UBrDYULY7PagbgUZLJH6QbYmJAECjuMigZEMVDsJzCFLNXxtajBBCcQQ0MwAUVWDEQNUgADVHBQGNJ3KAALygABEAAkYNAMOB4GRogLFFTBPB3AExcwABT0xnM9zsyhc9wASmCKhwDQ8ZC8iElzhB7Bo3zcZmY7AYzEg-Fg0HUiS58D0Ii8AoZTJZggFSRxAvADlQAHJhAA5SASAVBFQAeW+ZF2gldWkgx1QjgUrmkeFATgtOlGWH0KAQiBhwiudokkuiIgMHBx3RYbC43CCJUUqBaxn5AG9UPlBuxENIfgBfIcj6R80ATqecGdzsiLxTuGiwKficeT0AARkX+-hU5XJ6nF+Crn5sGCQA%20)

```javascript
type a = {t: number}
type b = {t: number}

let c: a = {t: 1}
let d: b = {t: 1}
// 这样是允许的
d = c
```

js 这样的做的是为了兼容js动态特性。

go 中不是这这样，比如

```go
type celsius float64
type fahrenheit float64
```

虽然上面两个自定义类型底层类型都是 float64，但他们不同的名字指示了两种类型具有不同的语义，进而操作上的不兼容。这是一个很有用的特性，可以进一步切分基本类型以便表达不同的语义。

1. 类型转换

js 中内置类型的构造函数本身也承担着类型转换的作用，比如：

```javascript
String(1)
// output '1'
```

在 go 中也类似，每一个自定义类型本身也是类型转换函数。

```go
package main

import (
  "fmt"
)

func main() {
  i := string(65)
  fmt.Println(i)
}
//output "A"
```

但是结果可能出乎意料，为什么不是 "65"。

_类型转换并不改变内存数据，只是换一种方式解释数据。_

记住这一重要原则。

至于如何按照我们设想的方式转换，在讲到基本数据类型时会详细说，这里先略过。

1. ts 中的interface 和 type 同为关键字，多数情况下是可以互换的：

```typescript
interface Animal {
    color: string;
    age: number;
}
// 等价于
type Animal = {
    color: string;
    age: number;
}
```

go 中的type 是声明自定义类型的关键字。

而 interface 是像 string、struct 等一样的内置类型。

```go
type Celsius float64
type Fahtenheit float64

const a Celsius = -273
const b Fahtenheit = 0

// 类似的 
type Reader interface {
  Read([]byte) int, error
}
// 表明 Reader类型是具有 Read 方法的方法集名
```

后面我们会看到 interface 就是为一个方法的集合起个别名。

## 变量

在 go 中，变量：

* 有一个具体类型
* 必有一个初始值

来详细说明下这两条原则。

类型决定了如何解读一段内存的数据。

在 js 中，运行时才能确定一个变量的类型，go 中声明时就要指定。 ts已经说明了这样做的诸多好处。

在 go 中有个零值机制，保障变量必有一个初始值。 具体来说，如果变量声明时没有同时赋值，那么就会被赋予该类型的零值，比如 int 类型的零值是 0，sting 类型的零值是 '' 等。

实践证明，如此不仅简化了代码，还让处理边界条件更容易，而js做不到这一点。

### 短变量声明

在滴一部分声明提到，const、var 关键字，go 为变量声明、指定变量类型、赋值这一连贯操作提供一个简化写法：

```go
a := 1
```

同时完成了 a 变量声明、赋值为 1、同时推导 a 的类型为 int。

当前作用域已经声明 a 后，这种写法并不会报错，而是等价于赋值。这种声明有一个名字：短变量声明。 之后在 go 代码中遇到最多的就是这种声明方式了。

## 赋值

这赋值方面，js 具备的能力 go 都具备，比如： 1、 `count *= scale` 避免在表达式中重复变量本身 2、数字变量的自增和自减

除此之外，比 js 还多一项能力：多重赋值。

### 多重赋值

指多个变量一次性被赋值。比如最常见的场景：交换变量。】

```go
x, y = y, x
```

多重赋值可以极大简化交换变量这种场景。

#### 应用场景

后面会提到go 的函数支持多值返回，多重赋值恰好可以承接多值返回。

eg，函数通过额外的返回值指示一些错误：

```go
_, err = io.Copy(dst, src) // 丢弃字节个数
_, err = x.(T) // 类型转换
```

最后关于赋值补充两个主题：

1、隐式赋值。程序中的很多赋值都是隐式进行的。比如：调用函数时的参数值传递。

2、可赋值。在 js 中不存在这个问题，但 go 中变量是有类型的，目前可以记住一个简单的规则：相同类型的变量才能互相赋值。

## 包和文件

比函数更大的作用域通常称为模块，es6 中文件即模块，模块承担着和函数类似的作用：封装和复用。

在go 中对应es6 中模块的概念是包。不过包的单元是文件夹。一个包等价于把包内各文件合并成一个大文件，所以变量可以在包内自由的跨文件共享。

慢慢你会发现这样设计的好处： 当随着功能的增多：拆分文件没有任何阻力，变量、函数不需要 export去 import 来。在 js 中完全是另一番场景。

go 为开发大型应用设计，重构会不断穿插在整个开发过程。此刻你会体会到 go 的第一条原则：

如果向好的方向发展，go 会尽力消除改变的阻力。相反的，坏味道的代码会被直接禁止。

## 项目组织

这是一个很大的主题，这里聚焦在依赖管理。

go 对代码的共享是文件级别的，这一直是我最欣赏的特点之一。 极大的便利了代码的共享，并且反过来促进了代码的复用性 —— 指不定其他项目可以 import 呢。 反过来看 js，为了共享要新建个 npm package、 npm publish、npm link... 简直头大。

不过 js 借助 git submodule 也可达到直接文件共享的效果。

我们回到依赖管理，一个典型的 go 项目文件结构：

```bash
├── LICENSE
├── README.md
├── buggy
│   └── buggy_test.go
├── go.mod
├── go.sum
├── quote.go
├── quote_test.go
```

注意里面两个文件：

* go.mod 这个等价于 package.json 
* go.sum 等价于 package-lock.json

### 包的初始化

包的初始化指的是在 import 包时执行的函数。

在 js 中是没有这个钩子的，当需要引入执行时我们可以写自执行函数。那这个钩子的设计是不是多余呢？

因为 init 函数的存在，显示的表明：包的引入即产生副作用。

