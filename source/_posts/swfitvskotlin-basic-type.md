title: Swift vs Kotlin 漫谈之基础类型

date: 2017-05-20 23:08:50
tags:

- swift vs kotlin

---

***Swift:***

Kotlin 君，又见面了。你知道 Swift 中 Array，Set，Dictionary都是值类型嘛？

***Kotlin:***

我们和你们不一样，我们的基础类型都是类。你看我们的 Int 类型的定义就是类

```kotlin
public class Int private () : Number, Comparable<Int> {
    companion object : IntegerConstants<Int> {}
 	...   
}
```

***Swift:***

你看我们的Int类型是这样的

```swift
public struct Int : SignedInteger, Comparable, Equatable {
	...
}
```

既然你们的基础类型是类，那么会有引用问题嘛？一个对象被多个其他对象持有这种情况？

<!--more-->

***Kotlin:***

基础类型没有提供修改接口，都是 `immutable` 类型，当值发生变化时都是生成了个新的对象，所以不会有引用问题。

***Swift:***

这样子，我明白了。说到这里我们来讲讲基础类型吧。

Swift 里基础类型有整数，浮点数，布尔，字符串，元组，可选，集合类型(Array, Set, Dictionary)

***Kotlin:***

你的的基础元素还真丰富啊，不过我们除了没有元组，其他也都有呢。元祖在kotlin有个类似的概念，我们可以通过析构一个数据类型实现类似的功能，如标准库里提供的`Pair`，`Triple`！

***Swift:***

我们先来说下整数类型吧！我们的整数有有符号整数Int，还有无符号整数UInt，一般情况下都是用Int，下面我们看看如何声明吧

```swift
let one = 1 //Int
let two = 2 //Int
let oneHundred = 100 //Int
let oneThousnad = 1_000 //Int
```
使用二进制、八进值或十六进制表示可以这样声明

```swift
let binaryInteger = 0b10001 //Int 17
let octalInteger = 0o21 //Int 17
let hexadecimalInteger = 0x11  //Int 17
```

***Kotlin:***

这个kotlin也是一样的写法，除了声明常量用`var`而不是`let`，另外kotlin不支持8进制。

有了整数就有浮点数，你们的浮点数是什么样的？

***Swift:***

我们的浮点数有 Float 和 Double，Float是32位的精确的到小数点之后6位，Double是64位的精确到小数点之后的15位。
当声明一个浮点数时，如果不指定类型，Swift会类型推断为 Doule

```swift
let p = 3.14 //Double
let pi = 3.1415926 //Double
let scale: Float = 1.0 //Float
```
你们呢？是不是和我们一样啊？

***Kotlin:***

kotlin `Float`类型必须加f或F, `val scale: Float = 1.0F`

***Swift:***

说到这里上周我为了一个选择抛硬币，所以这里要提下只拥有ture和false的Bool类型

```swift
var obverseSideOfCoin = true
var reverseSideOfCoin = false
```

就是因为有了Bool类型，所以我们才会有控制流呢！

***Kotlin:***

写程序最离不开的就是Boolean类型了吧，所有的语言都会支持。字符串我们也来讨论下吧，kotlin中字符串对象是不可变型，类是个final类型的class。

***Swift:***

嗯，我们Swift里字符串是改进很多的，这里我简单介绍下。

```swift
// 创建一个字符串
let string = "我是一个字符串"
//创建一个空字符串
let emptyString = String()
//判断字符串为空
if emptyString.isEmpty {
    print("empty")
}
```

上面声明的字符串是不可变类型的，创建之后就不能更改，如果我需要更改一个字符串应该这样创建

```swift
var swiftVSKotlin = "swift"
swiftVSKotlin += "VS"
swiftVSKotlin += "Kotilin"
```

***Kotlin：***

这里其实创建了三个对象，编译器优化的话就是可能就是一个对象

***Swift:***

嗯，我们的字符串也可以按字符来遍历

```swift
let animals = "🐶🐱🐭🐹🐰"
for animal in animals.characters {
    print(animal)
}
```

***Kotlin:***

kotlin有字符串模板概念，如

```kotlin
println("name=${user.name}")
```

`${expression}`直接求值输出

***Swift:***

我们是这样写的

```swift
let kotlin = "kotlin"
let swift = "swift"
let stirng = "\(kotlin)和\(swift)展开了一场切磋"
```
我们字符串比较也可以直接使用`==`来比较了，所以写OC的同学会犯错

***Kotlin:***

字符串拼接的写法还是有些差异的，我们单个变量的时候直接 `$` 后面加变量名就可以了。对于字符串的比较 Kotlin 也做了优化，可以直接用 `==` 来比较，也可以用 `===` 来进行引用的比较。不过我们这里还有一个更牛逼的字符串的写法，叫做 `raw string`。

```kotlin
val text = """
    for (c in "foo")
        print(c)
"""
```

`raw string` 用三重引号表示("""), 其内容不转义, 可以包含换行符和任意字符。怎么样，你们可以吗？

***Swift:***

这个我们没有，不过我们集合类型很强大，所以我们在函数式编程上有天然的优势呢，集合类型我就简单说下Array吧

```swift
var someInts = [Int]() //创建空数组
var threeDoubles = Array(repeating: 0.0, count: 3) //创建里面包含3个0.0的数组 [0.0, 0.0, 0.0]

let array = [1,2,3]
let array2 = [4,5,6]
let array3 = array + array2 // 可以通过两个数组相加创建一个新的数组
```

看 还是挺厉害的吧

***kotlin:***

确实简洁，我们也不弱

```kotlin
val a1 = arrayListOf(1, 2, 3)
val a2: IntArray = intArrayOf(1, 2, 3)
a2[0] = a2[1] + a2[2]
val a3 = a1 + a2
```

***Swift:***

高手对招果然痛快，哈哈。swift和kotlin都是很棒的语言。这次交流就这么多了，Swift在基础类型还有很多细节。下次在讲吧～


# 基础知识

## Koltin

在Kotlin中，所有东西都是对象，基本类型是**内置的优化过**的类。

```kotlin
/**
 * Represents a 32-bit signed integer.
 * On the JVM, non-nullable values of this type are represented as values of the primitive type `int`.
 */
public class Int private () : Number, Comparable<Int> {
    companion object : IntegerConstants<Int> {}
 	...   
}

/**
 * Represents a single-precision 32-bit IEEE 754 floating point number.
 * On the JVM, non-nullable values of this type are represented as values of the primitive type `float`.
 */
public class Float private () : Number, Comparable<Float> {
    companion object : FloatingPointConstants<Float> {}
 	...   
}
```

### kotlin中的数字类型

Type | Bit width
---- | ---
Double | 64
Float |  32
Long |  64
Int |  32
Short |  16
Byte |  8

和 java 类似，kotlin基本类型也是`Int`, `Long`, `Double`, `Float`, `Short`, `Byte`。这几种类型都继承类`Number`
所以他们拥有父类的几个方法

```kotlin
— toByte(): Byte— toShort(): Short— toInt(): Int— toLong(): Long— toFloat(): Float— toDouble(): Double
— toChar(): Char
```


### 基本类型的使用

声明变量用`var`或`val`

```kotlin
val a = 123	// Int
val b = 123L	// Long
val c = 123.5F	// Float
val d = 123.5		//Double
val e = 0x00ff	// 十六进制
val f = 0b0101	// 二进制

// tips: 不支持八进制
```

特殊用法：

**数字字面值中的下划线(自 1.1 起) 你可以使用下划线使数字常量更易读**:```kotlin      val oneMillion = 1_000_000val creditCardNumber = 1234_5678_9012_3456Lval socialSecurityNumber = 999_99_9999L
```

### 布尔

布尔用 Boolean 类型表示,它有两个值:`true` 和 `false`。 若需要可空引用布尔会被装箱。内置的布尔运算有:- `||` 短路逻辑或
- `&&` 短路逻辑与
- `!` 逻辑非


### 数组
数组在 Kotlin 中使用 `Array` 类来表示,它定义了 `get` 和 `set` 函数(按照运算符重载约定这会转变为 `[]` )和 `size` 属性,以及一些其他有用的成员 函数:我们可以使用库函数 `arrayOf()` 来创建一个数组并传递元素值给它,这样 `arrayOf(1, 2, 3)` 创建了 `array [1, 2, 3]`。


### 字符串

字符串用 String 类型表示。字符串是不可变的。字符串的元素字符可以使用索引运算符访问: s[i] 。可以用 for 循环迭代字符串:

```
for (c in str) {
	println(c)}
```

String的源码定义

```kotlin
/**
 * The `String` class represents character strings. All string literals in Kotlin programs, such as `"abc"`, are
 * implemented as instances of this class.
 */
public class String : Comparable<String>, CharSequence {
    companion object {}

    /**
     * Returns a string obtained by concatenating this string with the string representation of the given [other] object.
     */
    public operator fun plus(other: Any?): String

    public override val length: Int

    public override fun get(index: Int): Char

    public override fun subSequence(start: Int, end: Int): CharSequence

    public override fun compareTo(other: String): Int
}
```

#### 字符串模板
可以用`${}`包含任意表达式，如果表达式是单个变量，可省略`{}`如：

```kotlin
val s = "abc"
val str = "$s.length is ${s.length}"
// 求值结果是abc.length is 3

println("1 + 2 = ${1 + 2}")
// 输出 1 + 2 = 3
```

#### 原生字符串(raw string)

Kotlin 中存在两种字符串字面值: 一种称为转义字符串(escaped string), 其中可以包含转义字符, 另一种成为原生字符串(raw string), 其内容可以包含换行符和任意文本. 转义字符串(escaped string) 与 Java 的字符串非常类似:

```kotlin
val s = "Hello, world!\n"
```

原生字符串(raw string)由三重引号表示("""), 其内容不转义, 可以包含换行符和任意字符:

```kotlin
val text = """
    for (c in "foo")
        print(c)
"""
```

### 类型智能转换
在许多情况下,不需要在 Kotlin 中使用显式转换操作符,因为编译器跟踪 不可变值的 `is` 检查,并在需要时自动插入(安全的)转换:

```kotlin
fun demo(x: Any) {	if (x is String) {		print(x.length) // x 自动转换为字符串
	}}
```

### 相等性Kotlin 中有两种类型的相等性:
- 引用相等(两个引用指向同一对象)
- 结构相等(用 `equals()` 检查)#### 引用相等引用相等由 `===`(以及其否定形式 `!==` )操作判断。`a === b` 当且仅当 a 和 b 指向同一个对象时求值为 true。#### 结构相等

结构相等由 `==`(以及其否定形式 `!=` )操作判断。按照惯例,像 `a == b` 这样的表达式会翻译成```kotlin
a?.equals(b) ?: (b === null)
```
也就是说如果 `a` 不是 `null` 则调用 `equals(Any?)` 函数,否则(即 `a` 是 `null` )检查 `b` 是否与 `null` 引用相等。简单理解就是:
**`==`表示比较值， `===`表示比较引用**

```kotlin
val a = StringBuffer("aaa").toString()
val b = "aaa"

val r1 = a == b // 求值结果是true
val r2 = a === b // 求值结果是false

// 如果val a = "aaa", 则两个都输出true， 思考为啥会这样？
```


## Swift

Swift 拥有 C 和 Objective-C 语言支持的基础类型，比如Int，Float，String，Bool..同时根据语言特性对集合类型（Collection Types） Array， Set ，Dictionary 增加了扩展。

除了基础类型，Swift 还增加了元组(Tuples)和 Optional，Optional篇幅较大将于之后的章节详细介绍。

Swift 作为强类型（type-safe）的语言, 意味着在使用值时，编译器已经知道具体的数据类型。

| 类型 |  x |
| --- | --- |
| 整数 | Int UInt |
| 浮点数 | Double |
| 字符串 | String |
| 元组 | () |
| 可选类型 | Optional |
| 集合类型 | Array，Set，Dictionary |


### 整数 Integers

整数类型是没有小数点部分的数组，比如 1，0，-1。
Swift 中有 Int 和 UInt 两种类型分表表示有符号和没有符号类型。

```swift
let one = 1
let zero = 0
let negativeOne = -1
```

### 浮点数 Floating-Point Number

浮点数是有小数点部分的数字，比如 1.234567, 3.141597
Float 和Double 是 Swift 提供的两种浮点数类型。
Float类型有6位的精度和32位，Double类型有15位的精度和64位。

```swift
let pi = 3.1415926
let goldenRatio = 0.618
let floatValue: Double = 1.2345678
```

### 布尔 Booleans

布尔类型在 Swift 使用 Bool 来表示，Bool类型的值只有 true 和 false，一般用来做逻辑判断。

```swift
let toDoSomething: Bool = true
if toDoSomething {
    print("Hello wrold")
}
```

### 字符串 String

字符的有序集合表示为 String 类型，String 类型是 Character类型集合。

```swift
//创建字符串
let string： String = "我是一个字符串"
//创建空字符串
let emptyString = ""
let anotherEmotyString = String()
if emptyString.isEmpty {
	print("Empty")
}
```

创建一个可变字符串适用`var`来声明

```swift
var variableString = "Swift"
variableString += "vs Koltin"
```

Swift的 `String`类型是值类型，已经不是 OC 时代的 引用类型了。

```swift
public struct String {
	...
}
```
字符串的比较可以使用`==`号，


### 元组 Tuples
元组可以把多个值合并成一个值，比如

```swift
let train = (12306, "和谐号")
```
也可以在声明的时候分别给予名称

```swift
let metro = (number: 4, name:"4号线")
print("metro number: \(metro.number) name: \(metro.name)")
```

### 集合类型
集合类型是Swift 函数式编程不可获取的部分，在之后会更详细的讲解

#### 数组 Array

下面是数组的常用方法

```swift
//创建数组
var someInts = [Int]()

//插入数据
someInts.append(3)

//创建默认值数组
let fourInt = Array(repeating: 0, count: 4)

//数组合并
var newIntArray = someInts + fourInt

//判断数组是否为空
let isArrayEmpty = newIntArray.isEmpty

//通过下标访问数组

let firstElement = newIntArray[0]

//获取数组最后一位
newIntArray.last

//遍历数组

for value in newIntArray {
    print("value = \(value)")
}
//遍历数组2
for (index, value) in newIntArray.enumerated() {
    print("Item \(index + 1): \(value)")
}
```
