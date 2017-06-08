title: Swift vs. Kotlin 之变量定义
date: 2017-05-18 12:59:58
tags: 
- Swift
- Kotlin
- swift vs kotlin

thumbnail: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-170030487.jpg?imageView2/1/w/200
banner: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-170030487.jpg?imageView2/1/w/1024/h/460 

---


tags: Kotlin, Swift

Kotlin 君和 Swift 君在一个团队一起开发已经很久了，由于平台的差异性，他们经常会进行一些技术上的交流（PK），「Kotlin vs. Swift」课程就是他们在互相切磋是的语录。

<!-- more -->

## 变量定义

#### Kotlin：

你看下我们 Kotlin 定义变量太酷了，比我们之前用 Java 方便太多了，你们 Swift 声明变量方便吗？

#### Swift：

哦，你们是怎么样的？

#### Kotlin：

我们可以直接这样定义一个变量 `val b = 2`，Kotlin 可以自行推断变量的类型，要是 Java 就不行了，必须要给每个变量定义类型。

#### Swift：

我们这里也有类型推断的功能，我们声明一个变量直接 `let count = 10` 就可以了，和你们差不多。

#### Kotlin：

咦，你们用 `let` 声明变量，和我们不太一样。

#### Swift：

是的，我们用 `let` 声明常量，用 `var` 声明变量，而且我们常量还分为储值变量和计算变量。

#### Kotlin：

储值变量和计算变量？是个什么概念，我们这里没有。

#### Swift：

像 `var count = 10` 这样的定义就是储值变量。像

```
var <variable name>: <type> {
    get {
        <statements>
    }
    set(<setter name>) {
        <statements>
    }
}
```

这样定义为变量自定义了 getter 和 setter 方法，这样每次获取变量值得时候都要经过一些的计算才能得到变量的值，所以叫计算变量。

#### Kotlin：

这个和我们定义 getter 和 setter 方法一样吗。我们完整的变量定义是这样的

```
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```

其中 initializer、getter 和 setter 是可以省略的，但是我们没有计算变量这个概念。

#### Swift：

恩，我们定义变量的语法是差不多的，除了 `let` 和 `val` 的差别。

#### Kotlin：

哎，你们的计算变量能不能给自己赋值啊？如果可以赋值的话，和储值变量有什么差别呢？

我们 getter 和 setter 方法里面有个「支持域」的概念，可以通过「支持域」直接给变量赋值，所以重写 getter 和 setter 方法也只是实现不一样，本身没有质的差别。

#### Swift：

我们不行，在 getter 和 setter 只能通过另外一个储值变量来保存计算的结果。

#### Kotlin：

这点还是有差异的，因为你们 getter 和 setter 不能给当前变量赋值，只能进行计算的工作，所以你们才有计算变量的概念。

#### Swift：

应该是这样的。

## 知识点总结

### Kotlin

Kotlin 定义变量的完整语法

```
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```

其中 initializer、getter 和 setter 是并不是必须的。在属性类型可以通过 initializer 推断的情况下，属性类型也可以不声明。

```
val a: Int = 1  // 直接声明
val b = 2   // `Int` 类型是推断出来的
val c: Int  // 在没有初始化赋值的化，需要定义类型
```

Kotlin 制定定义只读变量(read-only variable，又叫常量)和变量(mutable variable)，分别用 `val` 和 `var` 关键字修饰。

```
val a: Int = 1 // 不可再次赋值
var b: Int = 1 // 可再次赋值
```

### Swift

Swift 声明常量，用 let 关键字，常量的值在初始化后不能被再次修改

```
let <constant name>[: <type>] = <expression>
```

Swift 声明储值变量，用 var 关键字，变量的值可以被任意修改

```
var <variable name>[: <type>] = <expression>
```

Swift 声明计算变量，用 var 关键字，同时需要指定 set 和 get 的方式，仅指定 get 则为只读计算变量

```
var <variable name>: <type> {
    get {
        <statements>
    }
    set(<setter name>) {
        <statements>
    }
}
```

Swift 支持类型推断，在有初始值的情况下，可以省去类型声明，例如：

```
let count: Int = 10 // 常量
var message: String = "Hello" // 变量
```

可以简写为

```
let count = 10 // Int 类型
var message = "Hello" // String 类型
```


## 欢迎大家关注我们的公众号

![](http://7xpox6.com1.z0.glb.clouddn.com/qrcode_for_gh_b2ad0581a6c4_430.jpg?imageView2/2/w/320) 
