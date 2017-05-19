title: Kotlin：The Good, The Bad, and The Ugly(译)
date: 2016-08-27 16:59:00
tags: 
- Android
- Kotlin
  
thumbnail: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-160635495.jpg?imageView2/1/w/200
banner: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-160635495.jpg?imageView2/1/w/1024/h/460 

---


tags: Kotlin, Android

在我的 [上一篇文章](https://medium.com/keepsafe-engineering/lessons-from-converting-an-app-to-100-kotlin-68984a05dcb6), 谈到了关于转换 Java 到 Kotlin 代码和我喜欢的一些库。现在，我想要谈谈关于 Kotlin 这门语言本身的想法，还有它和 Java 交互的方式。

<!-- more -->

## The Good

Kotlin 有许多让人喜欢的理由。像 `null safety`、`property access` 和 `unchecked exceptions` 类似这些明显的特性在 [publications](https://blog.jetbrains.com/kotlin/2016/01/kotlin-digest-2015/) 都有描述，我就不重复了。我只讲一些我真正喜欢但很少被提到的特性。

### Automatic conversion of Java to Kotlin

JetBrains 在 IntelliJ 中集成了 `Java to Kotlin converter` 为我们节约了大量的时间。虽然它还不是很完美，但是它让你不用再重复输入。要是没有它，你将要花费大量的时间从 Java 代码转换成 Kotlin。

### lateinit, Delegates.notNull and lazy

Kotlin 的 `null safety` 非常好，但是由于在 Android 的 Activity 生命周期的设计，你常常不得不在 *onCreate* 这样的回调中初始化一个变量，而不是在类的构造函数中。假设你有一个属性需要定义，你肯定想要这样：

```
val name: String
```

如果你必须在 `onCreate` 中初始化这个属性，就不能用 `val` 定义，而必须使用 `var`。但是这样你必须在定义的时候为该属性提供一个值进行初始化，或者将它定义为可空类型：

```
var name: String? = null
```

这样的确有效，但是当你每次使用它的时候都要进行空检查。虽然 Korlin 提供了友好的的空断言，但是在实践中即使你知道你的属性不会为空，你也不愿意到处使用 `!!`。庆幸的是，Kotlin 提供了更好的方式：[*lateinit*](https://kotlinlang.org/docs/reference/properties.html#late-initialized-properties) 和 [*Delegates.notNull*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/not-null.html)。任何一个都能可以让你在不初始化的情况下定义一个非空类型。
 
```
lateinit var name: String
var age: Int by Delegates.notNull<Int>()
```

这两种方式中，当你尝试在初始化之前访问该属性都会抛出异常。除了`lateinit` 不能用于基础类型的定义，这两种方法没有什么大的差别。

你还有第三种选择就是使用 `lazy` 委托。如果一个属性能够利用其他属性或方法获得数据进行初始化，那么 `lazy` 会是一个很好的选择。类似这样：

```
val imm: InputMethodManager by lazy { 
    getSystemService(INPUT_METHOD_SERVICE) as InputMethodManager 
}
```

上面块里面的代码在第一次读取之前并不会执行，执行的结果会被保存起来以供后面使用，所以后面的代码块只会被执行一次。

### Functional collection extensions

Kotlin 提供为集合和迭代类提供了大量的函数式 [扩展方法](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index.html#functions)。像 [*any*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/any.html)、 [*joinToString*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html) 和 [*associate*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate.html) 的方法能够帮助节约大量的时间，不用像 Java 里面一样手工编写 *for* 循环去实现。

Kotlin 还提供了大量的函数式集合操作的懒加载模式，在这种模式下载每个操作执行之前并不会进行集合的拷贝，但是在我的接受范围内，发现懒加载和即刻加载在性能上并没有什么大的差别。


###  Named and default function arguments



[命名参数](https://kotlinlang.org/docs/reference/functions.html#named-arguments) 和 [默认参数](https://kotlinlang.org/docs/reference/functions.html#named-arguments) 是非常基础的，但它们让你不再需要重载方法，并且也替代了 Builder 模式的一种使用场景。

根据具体使用场景，你甚至可以在依赖注入中将生产依赖作为默认参数，然后在测试的时候传入模拟的数据。

例如，你在 `presenter` 中需要一些全局状态，你可以这样定义构造函数：

```
class Presenter(
        val okhttp: OkHttp = productionOkHttp(),
        val picasso: Picasso = productionPicassoInstance()
) {...}
```

这样，你就你可以 `UI` 代码中创建 `presenter` 实例的时候不传递任何参数，但是在测试的时候可以传递模拟的实例作为参数。完整的依赖注入框架会更加强大，但这是一个一些简单语言构造的很好的例子。

## The Bad

尽管 Kotlin 非常棒，但是它并不完美。我列举了一下我不喜欢的部分。

### No namespaces

Kotlin 允许你在文件中定义顶级的函数和属性。这是一个非常棒的特性，但是这会带来所有从 Kotlin 引用的顶级声明无法区分的困扰。有时，这让我们在读代码的时候很难快速确定用的是哪一个函数。

例如，你定义这样一个顶级函数：

```
fun foo() {...}
```

你可以通过 `foo()` 调用。如果你在不同的包里面也存在同样的方法，在调用侧不能明显区分出是调用的哪个方法。你可以通过在前面添加包名的方式去调用，但是如果 Java 约定的包名很深，似乎不太好。

一种近似的解决方案是使用单例的 `object` 类。

```
object FooActions {
    fun foo() {...}
}
```

这样你在 Kotlin 中可以通过 `FooActions.foo()` 调用，但是在 Java 代码中就不是那么友好了。在 Java 中你必须要这样 `FooActions.INSTANCE.foo()` 这样调用，这看起来并不完美。你可以使用 `@JvmStatic` 去注解你的方法从而省掉 `INSTANCE`，这是你能做到的最好结果。这并不是什么大不了的事，但是如果 Kotlin 能够提供命名空间的话，能省不少事。

### No static modifier

无独有偶，Kotlin 提供为静态函数和属性提供了一个和 Java 不一样的处理方式。并不是说有多烂，只是觉得让代码变得不干净而且没有必要。例如，在 Android 的 `View` 类中定义的静态属性 `View.VISIBLE` 和静态函数 `View.inflate`。

```
public class View {
    public static final int VISIBLE = 0x00000000;
    public static final int INVISIBLE = 0x00000004;
    public static View inflate(Context context, int resource) {...}
}
```

这个定义是简单的。然而，在 Kotlin 代码中：

```
class View {
    companion object {
        @JvmField 
        val VISIBLE: Int = 0x00000000
        @JvmField 
        val INVISIBLE: Int = 0x00000004
        @JvmStatic
        fun inflate(context: Context, resource: Int) {...}
    }
}
```

尽管 Kotlin 的版本并没有那么恐怖，但是它的复杂程度超过了我对这门语言的预期。如果把注解去掉，那么你在 Java 代码中不得不使用这样可怕的语法去调用：

```
// With annotations:
View.VISIBLE;
//Without annotations:
View.Companion.getVISIBLE();
```

没有更好的方式去创建静态函数和属性让我感觉很奇怪。我知道 `companion objects` 是真正的对象并且能够用来实现接口，但是这并不能足够说明能完全替代普通的静态声明。

### Automatic conversion of Java to Kotlin

在我的第一篇帖子里这是我列出来的我喜欢 Kotlin 的愿意之一，并且它很好用。但是正由于它在 80% 的时候都运行的很好，它在一些场景的失败实在令人沮丧。

Java 文档经常错位，特别是在段落横跨了好几行的时候。静态域和方法被转换成 `companion object`，除非你手动添加为他们分别添加 `@JvmField` 和 `@JvmStatic` ，你之前 Java 调用代码不在有效而出错。

由于 Kotlin 团队花了大量的时间在转换代码上，我相信这些问题一定会被修复的，因此我对这些问题保持乐观。

### Required property accessor syntax

Kotlin 提供一个很棒的语法糖叫做「属性访问语法」，它让你可以像访问 Kotlin 属性一样访问 `JavaBeans` 类型的 `getters` 和 `setters` 方法。例如，你可以这样 `activity.context` 调用 `Activity.getContext()`，而不用写整个方法名。如果你在 Kotlin 使用传统的方式调用，lint 会给你一个警告告诉你使用「属性调用语法」。

这是一个很好的特性，但是有时候我的方法名以 `get` 开始，但是并不想使用「属性调用语法」。一个很常见的例子就是 Java 的原子类。如果你有一个变量 `val i = AtomicInteger()`，你可能想通过 `i.getAndIncrement()` 调用。但是 Kotlin 会想让你用 `i.andIncrement` 这种方式调用。这明显是画蛇添足。

你可以在每个调用的地方加上 `@Suppress(“UsePropertyAccessSyntax”)`，但很丑。如果你可以为这个函数添加一个注解告诉 linter 不要把它当做一个属性会更好。

### Method count

用 Kotlin 写代码肯定会减少你项目中的代码行数。但是它也会提高你的代码在编译以后的方法数。有很多原因导致这一点，但是其中一个主要原因就是 Kotlin 属性的实现方式。

和 Java 不一样，Kotlin 没有提供单独定义域的方式。你必须使用 `val` 或者 `var` 来声明变量。这样有一个好处，就是你可以随意为一个属性添加 `get` 或 `set` 方法而不会破坏其他地方对该属性引用的代码。这个特性省去了像 Java 一样定义 `getters` 和 `setters` 方法。

尽管如此，这个特性需要一定的成本。每一个公开的 `val` 变量都会导致 Kotlin 生成一个「支持域」和一个能被 Java 调用的 `getter` 方法。每一个公开的 `var` 变量都会生成 `getter` 和 `setter` 方法。庆幸的是，私有属性的 `getters` 和 `setters` 会生成域而不是生成方法。如果你之前的 Java 代码中定义了大量的公开域（这在定义常量的时候很常见），你会惊奇的发现方法大幅上升。

如果你的 Android 应用快接近方法数限制了，我建议你为不需要自定义 `getter` 方法的常量加上 `@JvmField` 注解。这样会阻止  `getters` 方法的生成，从而减少你的方法数。「更新：Kirill Rakhman 在评论中指出，你可以使用 `const` 修饰符替代 

不过其实没有那么糟。就像我在 [converting an app to 100% Kotlin](https://medium.com/keepsafe-engineering/lessons-from-converting-an-app-to-100-kotlin-68984a05dcb6) 文章里讨论过的，Kotlin 的标准库非常小，并且能够替代 Java 的许多常用库，这些库通常都更大，现在你再也不需要他们了。多亏了 Kotlin 的标准库，在从 Java 全部转换到 Kotlin 以后方法数反而减少了。只要你控制不会出现大范围的方法数提升，就不会有什么问题。

## The Ugly

最后，Kotlin 有两个设计我不是很认同，而且我不期望这个在未来会有什么改变。

### SAM conversion and Unit returning lambdas

这真是一个莫名其妙的设计。

可以嵌入 lambda 表达式是 Kotlin 最好的特性之一。如果有一个 Java 函数，它只有一个 SAM 接口（只有一个抽象方法的接口）：

```
public void registerCallback(View.OnClickListener r)
```

无论是 Java 还是 Kotlin，你都可以传递一个普通的 lambda 表达式去调用它。

```
// Java
registerCallback(() -> { /** do stuff */ })
//Kotlin
registerCallback { /** do stuff */ }
```

这的确很棒。但当你尝试去用 Kotlin 去定义类似的方法是莫名的困难。从 Java 测调用没有什么不同，但是当从 Kotlin 调用时需要明确指定类型。

```
fun registerCallback(r: View.OnClickListener)
// Kotlin. Note that parenthesis are required now.
registerCallback(View.OnClickListener { /** do stuff */ })
```

不得不说这很烦人，特别是当你从 Java 代码转换到 Kotlin 从而导致 Kotlin 代码不能再正常运行的时候。

常见的方式是用函数类型定义：

```
fun registerCallback(r: () -> Unit)
```

 这样用 Kotlin 调用起来会很方便，但是由于所有的 Kotlin 函数都需要一个返回值，这导致用 Java 调用该函数的时候变得很糟。你不得不显式地从 Java 表达式返回 `Unit`，这导致 lambda 表达式不可用：
 
```
 registerCallback(() -> {
    /** do stuff */
    return Unit.INSTANCE;
})
```

如果你在用 Kotlin 写库的话，根本找不到一个好的方式去实现一个高阶函数同时让 Java 和 Kotlin 都能方便的调用。在我的 [FlexAdapter](https://github.com/ajalt/flexadapter) 库里面，我尝试为为每个方法重载 `SAM interface` 或者 Kotlin 函数类型的参数。这样无论用这两种的哪种语言调用都很方便，但是库的 API 变得不简洁。

希望 Kotlin 的设计者们能够改变他们的想法在将来允许 `SAM` 转化成 Kotlin 的函数定义，但是我并不抱什么希望。

### Closed by default

到目前为止我说的所有关于 Kotlin 的缺点基本都是小的语法细节上的不简洁，并不是什么大事。但是，有一个设计在将来有可能导致巨大的痛苦：所有的类和方法默认都是封闭的。这种做法是被 `Effective Java` 里所推崇的，理论上听起来也很有道理，但对于任何一个需要使用一个有缺陷的第三方库的人来说都是一个坏的选择。

> 把所有的叶类都设置成静态的。毕竟你在完成这样一个项目——没有人能够通过扩展你的类的方式来完善你的工作成果。或许是由于安全原因——毕竟，`java.lang.String` 是 `final` 不就是由于这个原因吗？如果你项目的其他的成员向你抱怨，就告诉他们这样能提高执行效率——[*Roedy Green, How to Write Unmaintainable Code*](http://www.mindprod.com/jgloss/unmaindesign.html)

Kotlin 的文档里面的确有文章尝试去抵制这一决定，所以我把他们说的三个理由列出来。

### “Best practices say that you should not allow these hacks anyway”

关于对继承封闭的论据基本是围绕「Fragile Base Class Problem」展开，它认为如果允许在你的库的基础上继承出子类，他们可能改变代码运行的方式从而导致一些 bug。然而这只是一种可能性，会导致库运行异常从而导致 bug 的方式实在太多了。如果你重写一个类的功能，很明显你应该为破坏代码的运行负责。

我之所以用「很明显」是因为重写一个库的功能是很明确的该有使用方自己负责。我已经辅导计算机科学学生很多年了，他们会范所有你能想象到的错误，但是他们从不会因为重载一个方法导致的破坏感到奇怪。实在有太多不经意的方式会导致对依赖库使用的破坏，例如你传递的参数类型是对的但单位却传错了，或者你忘了调用一个必须调用的方法。

我欣赏那种减少代码被破坏可能性的编码方式，把类设置成不可变的的确能达到效果。但可以确定的是所有依赖库一定不是完整的或者是存在缺陷的，你又不可避免的要使用这些依赖库。为了修改一个封闭类，人们常常会使用一些 hack 的方法，这经常会仅仅是重写一两个类或者方法带来的 bug 更多。如果你不相信我说的话，这里有一个活生生的例子，如果你是一个 Android 开发者的话，你应该印象深刻：

AppCompat 23.2.0 终于把 [VectorDrawables](https://plus.google.com/+AndroidDevelopers/posts/iTDmFiGrVne) 加到 support 包里了。由于可以帮助减少 APK 的体积和内存的占用，要不是它有一个 bug [会导致在 Activity 里面导致内存泄露](https://code.google.com/p/android/issues/detail?id=205236)
，本应该收到广泛欢迎。这个支持包在 [几周后被移除](https://plus.google.com/+AndroidDevelopers/posts/iTDmFiGrVne)。

内存泄露是怎么导致的呢？为了 [提高 VectorDrawable 填充性能](https://medium.com/@chrisbanes/appcompat-v23-2-age-of-the-vectors-91cbafa87c88)，这个支持包的作者们需要改进 `Context.getDrawable` 的实现。但是这个方法是不可变的（final），所以他们不得不为每一个视图创建一份 `Resources wrapper` 的拷贝来处理 *VectorDrawables*。且不说这带来了大量的工作，这也导致大量的 `wrapped Resources` 变得不同步和为了复制产生的大量内存开销。如果那个方法不是不可变的，他们就不会这样胡搞了。

### “People successfully use other languages (C++, C#) that have similar approach”

人们在 Python 这样的语言可以在任何时候做任何修改。Python 也有像 `_asdict` 这样「非公有」不会在文档里描述的方法。它也有像 `__intern` 这样的 [name mangled](https://zh.wikipedia.org/wiki/Visual_C%2B%2B%E5%90%8D%E5%AD%97%E4%BF%AE%E9%A5%B0) 的函数，很难被发现。你可以自由的用 monkey-patch 或者重写任何一个你想重写的方法，Python 并不禁止这样做。


在我五年全职开发 Python 的期间，我从没有想过谁会通过重写方法破坏我的代码。我能想象在大多数情况下，用正确安全的方法去改变一个私有方法比由于 Python 的禁止而不得不重新实现一个同样的功能更加节约时间。

我并不是说要盲目地把每个类的实现都要改一遍，但是没有理由当我想这么做的时候却做不了。在 Python 社区里面有一句俗语 “We’re all consenting adults here”。你想对我的类做任何修改都可以。

### “If people really want to hack, there still are ways: you can always write your hack in Java and call it from Kotlin (see Java Interop), and Aspect frameworks always work for these purposes”

这真是一个荒诞的论点。即使是用 Java 如果你不用很难令人接受的反射的话，你依然不能重写封闭的 Kotlin 函数，所以这个论调无足轻重。

不能对依赖库进行扩展意味着想要添加任何新的特性和修改 bug 都很难。现实中，大多数库都需要使用一些黑客的手段。这就是现实，而且不会改变。任何库的作者都不能预测所有用户可能碰到的场景。所有的类都是不可变的只能让库的使用者实现库本身没有的功能的时候变得更加困难。相对于 Kotlin 其他语言特性的便利性，这个设计实在是太令人费解了。

如果你在编写一个 Kotlin 的依赖库，请把你所有的公开方法都设置成开放的。这会让你的用户更加方便。

## Conclusion

Kotlin 是一门非常棒的语言。它比 Java 简洁多了，它还有一个非常优秀的标准库，有了这个标准库你就可以将大量为了维持 Java 勉强可用下去的库都删掉了。多亏了代码自动转换功能，你可以很方便的把 Java 代码转换成 Koltin 代码，并且这个功能会越来越完善。如果你是一名 Android 开发者，你真应该去试一试。

原文链接：[Kotlin：The Good, The Bad, and The Ugly(译)](http://ohmerhe.com/2016/08/27/kotlin_good_bad_bugly/)

英文原文：[Kotlin: The Good, The Bad, and The Ugly](https://medium.com/keepsafe-engineering/kotlin-the-good-the-bad-and-the-ugly-bf5f09b87e6f#.s0t91g9xn)


## 欢迎大家关注我们的公众号

![](http://7xpox6.com1.z0.glb.clouddn.com/qrcode_for_gh_b2ad0581a6c4_430.jpg?imageView2/2/w/320) 

## 参考资料 
- **[1]** In the case of a *lateinit *property,*kotlin.UninitializedPropertyAccessException *will be thrown, where the*Delegates.notNull *will throw an *IllegalStateException*.
- **[2]** There are some details about *lateinit *that are worth noting, especially if you plan on accessing a *lateinit *property from Java code. First is that *lateinit*cannot be applied to primitive types such as *Int *or *Double*. The second is that a *lateinit *property is backed by a field with the same visibility as the property, and this field is visible from Java. Additionally, that backing field can be freely set to null from Java. If any of those are issues for your use case, *Delegates.notNull *may be a better choice.
- **[3]** The lazy *Sequence *operators can outperform the eager versions by up to 20%, but only once list sizes start growing very large. For lists under a megabyte or so in size, the lazy versions often perform the same or worse than the eager versions.
- **[4]** This is a bit of a simplification. Kotlin will only generate a backing field if you don’t define a get function, or if the defined get function doesn’t reference the implicit* field *identifier.
