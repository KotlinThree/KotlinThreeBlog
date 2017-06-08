title: Swift vs. Kotlin 漫谈之函数定义
date: 2017-05-22 13:07:40
tags: 
- Swift
- Kotlin
- swift vs kotlin

thumbnail: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-160648251.jpg?imageView2/1/w/200
banner: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-160648251.jpg?imageView2/1/w/1024/h/460 

---


tags: Kotlin, Swift

Kotlin 君和 Swift 君在一个团队一起开发已经很久了，由于平台的差异性，他们经常会进行一些技术上的交流（PK），「Kotlin vs. Swift」课程就是他们在互相切磋是的语录。

<!-- more -->

## 技术漫谈

**Swift：**

Hi，又见面了。

**Kotlin：**

恩，上次没分出胜负，这次再来。

**Swift：**

好，今天讲讲函数，你们是怎么定义函数的呀？

**Kotlin：**

我们是这样定义的

```
fun <functionName>(<parameters>)[: <Type>] {
    <block>
}
```

你们呢？

**Swift：**

我们是这么定义的

```
func <function name>(<parameters>) -> <return type> {
    <statements>
}
```

**Kotlin：**

区别就是个 `->` 和 `:` 的区别啊，其它都一样。

**Swift：**

嗯嗯嗯。还有个 `func` 和 `fun` 的区别。

**Kotlin：**

我们还可以用「单一表达式」直接声明函数，像这样：

```
fun <functionName>(<parameters>)[: <Type>] = <singleExpression>
```

在使用「单一表达式」来声明函数的时候返回的类型可以被推断出来，所以可以忽略不写。

**Swift：**

牛X。

**Kotlin：**

服不？

**Swift：**

不服，说说函数参数吧。

**Kotlin：**

Kotlin 中函数的参数使用帕斯卡尔（Pascal）符号定义，例如 `name: type`，使用逗号分割不同的参数，必须明确定义参数的类型。

**Swift：**

Swift 的参数也差不多，有外部名和内部名之分，外部名就是实参名，内部名就是形参名。

**Kotlin：**

那是咋定义的？

**Swift：**

```
func f(valueA x: Int, valueB y: Int) {

}
```
这里 valueA 和 valueB 就是外部参数名，x 和 y 是内部参数名  
函数在调用时要写上外部参数名

```
f(valueA: 1, valueB: 2)
```

**Kotlin：**

如果不想写参数名呢？

**Swift：**

那在定义的时候用 _ 指代外部名就行了

```
f(_ x: Int, _ y: Int) {

}

f(1, 2) // 函数调用
```

**Kotlin：** 
 
666666666666

**Swift：**

你们的参数可以加默认值吗？

**Kotlin：**

可以呀，后面 `=` 一个值就行

```
fun read(off: Int = 0) {

}
```

**Swift：**

我们也是一模一样的。

**Kotlin：**

赞

**Swift：**

再说说可变参数吧，我们可变参数的定义是在参数类型后面加三个点`...`，然后这个参数就可以当做数组使用了

**Kotlin：**

我们是用 `vararg` 关键字

**Swift：**

例如：

```
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// 结果是 3.0

arithmeticMean(3, 8.25, 18.75)
// 结果是 10.0
```

**Kotlin：**

我们是这样的

```
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts is an Array
        result.add(t)
    return result
}
```

**Swift：**

就是关键字不一样，其它还是非常相似的。

**Kotlin：**

恩，惺惺相惜。

# 知识点总结

## Swift 中函数的定义

```
func <function name>(<parameters>) -> <return type> {
    <statements>
}
```

如果函数没有返回值，则定义方式如下：

```
func <function name>(<parameters>) {
    <statements>
}
```

例如：

```
func f(x: Int, y: Int) -> Int {    
	return x + y  
}

f(x: 1, y: 2) // 函数调用
```

## Swift 中函数的参数

函数参数声明方式和声明变量相同，不过函数参数有外部名（实参名）和内部名（形参名）之分

```
func f(valueA x: Int, valueB y: Int) {

}
```
这里 valueA 和 valueB 就是外部参数名，x 和 y 是内部参数名
函数在调用是必须写上外部参数名

```
f(valueA: 1, valueB: 2)
```

如果想要函数在调用时省略外部参数名，则可以在函数声明时把外部参数名指定为 _（下划线）

```
f(_ x: Int, _ y: Int) {

}

f(1, 2) // 函数调用
```

如果不区分外部和内部参数名，则外部和内部参数名相同，例如

```
func f(x: Int, y: Int) -> Int {    
	return x + y  
}
```
上面这个函数的外部参数名和内部参数名都是 x 和 y

## 可变参数

函数参数类型后面加三个点"..."，则可把这个参数声明成可变参数
例如：

```
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// returns 3.0

arithmeticMean(3, 8.25, 18.75)
// returns 10.0
```

## 函数参数默认值

函数的参数可以指定默认值，这样这个函数在调用是可以省略这个参数
例如：

```
func f(x: Int, y: Int = 3) -> Int {    
	return x + y  
}

f(x: 1)
// 结果为 4
```

## Kotlin 函数定义

Kotlin 用 `fun` 关键字来声明函数，常见的是用块（block）来声明函数：

```
fun <functionName>(<parameters>)[: <Type>] {
    <block>
}
```

还可以用「单一表达式」直接声明函数：

```
fun <functionName>(<parameters>)[: <Type>] = <singleExpression>
```
在用「块」来声明函数的时候，返回的类型只有在没有返回（Kotlin 默认返回 Unit）的时候可以不定义。在使用「单一表达式」来声明函数的时候返回的类型可以被推断出来，所以可以忽略不写。

## Kotlin 中函数的参数

Kotlin 中函数的参数使用帕斯卡尔（Pascal）符号定义，例如 `name: type`，使用逗号分割不同的参数，必须明确定义参数的类型。

### 默认参数

Kotlin 的函数中允许直接为参数提供默认值，在调用的时候如果使用默认值可以忽略这个参数不传递。默认参数的定义方式是在类型定义后面用 `=` 传递默认值。

```
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size()) {
...
}
```

### 可变参数

对于参数数量可变的情况，在 Kotlin 中可以用 `vararg` 关键字来标记，和 Java 里面  `...` 是一个用途。

```
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts is an Array
        result.add(t)
    return result
}
```

然后调用：

```
val list = asList(1, 2, 3)
```

关于函数相关的知识，无论是 Swift 还是 Kotlin 都还有很多东西可以 PK，不过这篇这是基础语法篇，想要了解更多的和函数相关的 PK，敬请期待后面的更新。

---

![](http://7xpox6.com1.z0.glb.clouddn.com/kotlin-three-wechat.jpg)
