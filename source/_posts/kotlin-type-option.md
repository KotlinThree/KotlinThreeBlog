title: NullPointException 利器 Kotlin 可选型
date: 2016-07-14 18:52:57
tags: 
- Kotlin
  
thumbnail: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-138093189.jpg?imageView2/1/w/200
banner: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-138093189.jpg?imageView2/1/w/1024/h/460 

---


tags: Kotlin

NullPointException (简称 NPE ) 被称作 [The Billion Dollar Mistake](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions) 一直困扰着Java 和 Android 开发者。Kotlin 的类型系统中提供可选类型用于减少 NPE 问题带来的风险。

<!-- more -->

虽然，Kotlin 提供了可选类型用于减少 NPE 问题的风险，但是并没有办法完全消除 NPE 带来的隐患，本问将探讨如何巧妙地使用「可选型」更好的规避 NPE 的发生。

## 可选型定义

### 非空类型

我们先从可选型的定义开始，当我们在 Kotlin 中定义一个变量时，默认就是非空类型的，当你将一个非空类型置空的时候，编译器会告诉你这不可行。

```kotlin
var a: String = "abc"
a = null // compilation error
```

因此，如果你后面任何时候使用该变量时，都可以放心的使用而不用担心会发生 NPE。所以要想远离 NPE，首先需要**「尽可能的使用非空类型的定义」**。

### 可选型（可空类型）

虽然「非空类型」能够有效避免 NPE 的问题，但是有时候我们总不可避免的需要使用「可选类型」。在定义可选型的时候，我们只要在非空类型的后面添加一个 `?` 就可以了。

```kotlin
var b: String? = "abc"
b = null // ok
```

在使用可选型变量的时候，这个变量就有可能为空，所以在使用前我们应该对其进行空判断（在 Java 中我们经常这样做），这样往往带来带来大量的工作，这些空判断代码本身没有什么实际意义，并且让代码的可读性和简洁性带来了巨大的挑战。在网上可以看到许多人针对如何减少 NPE 提出了自己的建议，有的的确很不错，但成本依然很大。除此之外，还有一个最可恶的场景「我们会忘记」。

Kotlin 为了解决这个问题，它并不允许我们直接使用一个可选型的变量去调用方法或者属性。

```kotlin
val l = b.length // compilation error
```

你可以和 Java 中一样，在使用变量之前先进行空判断，然后再去调用。如果使用这种方法，那么空判断是必须的。

```kotlin
val l = if (b != null) b.length else -1
```

**注意： 如果你定义的变量是全局变量，即使你做了空判断，依然不能使用变量去调用方法或者属性。**这个时候你需要考虑使用下面的介绍的方法。

Kotlin 为可选型提供了一个安全调用操作符 `?.`，使用该操作符可以方便调用可选型的方法或者属性。

```kotlin
val l = b?.length
```

这里 `l` 得到的返回依然是一个可选型 `Int?`。

Kotlin 还提供了一个强转的操作符 `!!`，这个操作符能够强行调用变量的方法或者属性，而不管这个变量是否为空，如果这个时候该变量为空时，那么就会发生 NPE。所以如果不想继续陷入 NPE 的困境无法自拔，请不要该操作符走的太近。

## `Elvis` 操作符

上面有提到一种情况，当 `b` 为空时，返回它的长度值给一个默认值 -1。要实现这样的逻辑当然可以用 `ifelse` 的逻辑判断实现，但 Kotlin 提供了一个更优雅的书写方式 `?:`。

```kotlin
val l = b?.length ?: -1
```

`b?.length ?: -1` 和 `if (b != null) b.length else -1` 完全等价的。

其实你还可以在 `?:` 后面添加任何表达式，比如你可以在后面会用 `return` 和 `throw`（在 Kotlin 中它们都是表达式）。

```kotlin
fun foo(node: Node): String? {
  val parent = node.getParent() ?: return null
  val name = node.getName() ?: throw IllegalArgumentException("name expected")
  // ...
}
```

## `let` 函数

`let` 是官方 `stdlib` 提供的标准函数库里面的函数，这个函数巧妙的利用的 Kotlin 语言的特性让 `let` 接受的表达式参数中的调用方是非空的。

```kotlin
val listWithNulls: List<String?> = listOf("A", null)
for (item in listWithNulls) {
    item?.let { println(it) } // prints A and ignores null
}
```

上面代码的只会输出 `A`，而不会输出 `null`。

**需要注意的是，这个方法调用的时候必须要使用 `?.` 操作符调用才能生效哦。**如果你的部分代码依赖于一个可选型变量为非空的时候，就可以使用 `let` 函数。

参考这个函数的实现，下面我尝试提供几个自己定义的方法。

## 自定义处理

这里定义的两个方法是参考 `Swift` 里面的 `if let` 和 `guard` 进行的抽象。

### `orElse` 函数

`orElse` 是和 `Elvis` 函数结合使用的，默认 `Elvis` 后面只能直接或者执行一个表达式获取返回值或者直接通过 `return` 或者 `throw` 结束当前函数的执行。结合 `orElse` 函数，你能够更加灵活的处理前面的 `null`。

- 你可以处理一些逻辑以后，再返回一个可用的值。

```kotlin
var a:String? = null
var b = a ?: orElse {
	// 做任何事
   return@orElse "s"
}
```

- 也可以处理一些逻辑后， 通过`return` 或者 `throw` 结束当前函数的执行。

```kotlin
var a:String? = null
var b = a ?: orElse {
	// 做任何事
   return
}
```

### `guard` 函数

`Elvis` 默认只能对单个变量或表达式是否为空进行处理，当碰到多个变量需要一起判断时，就会束手无策，`guard` 就是为了解决这个问题。

```kotlin
fun testGuard(a: String?, b: String?, c: String?){
	guard(a, b, c) ?: orElse {
        print("a or b or c is null 
")
        return
    }
    // 现在 `a`，`b`，`c` 都是不为空
}
```

由于没有编译器的支持，所以暂时还不能实现 [空屏蔽](https://kotlinlang.org/docs/reference/null-safety.html#checking-for-null-keyword--in-conditions)。

这里定义的两个函数的实现，你可以自己尝试去实现一下，就当是个练习（鬼笑）。[AndroidExtension](https://github.com/KotlinThree/AndroidExtension)有具体的实现代码。

## 总结

经过一系列分析以后，我们已经对怎么使用好 Kotlin 可选型有一定的了解，如果不想 NPE 问题不断困扰，可以参考这里总结的几条。

- 尽可能的使用非空类型的定义
- 远离 `!!`，如果非要用，请调用代码在前面「三行之内」进行非空判断
- 熟练使用 `Elvis` 操作符
- 自定义一些常用的函数，让自己的代码更流畅

## 参考资料

- [null-safety](https://kotlinlang.org/docs/reference/null-safety.html)
- [trailing-closures-in-guard](https://github.com/apple/swift-evolution/blob/master/proposals/0056-trailing-closures-in-guard.md)
