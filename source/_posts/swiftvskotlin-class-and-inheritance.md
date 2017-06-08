title: Swift vs Kotlin 漫谈系列之类与继承

date: 2017-06-08 11:22:29
tags:

- swift vs kotlin

---

Kotlin 君和 Swift 君在一个团队一起开发已经很久了，由于平台的差异性，他们经常会进行一些技术上的交流（PK），「Kotlin vs. Swift」课程就是他们在互相切磋是的语录。内容会由简及深，慢慢深入。

# 技术漫谈

**Kotlin:**

Swift 君，你好。😏

**Swift:**

Kotlin 君，你好。🙂，干嘛笑的那么坏。

<!--more-->

**Kotlin:**

没有什么，你最近是不是胖了。🙃

**Swift:**

😓赶紧进入正题吧，说说你们的类是怎么定义的。

**Kotlin:**

👌我们定义类和 Java 差不多，也是用 `class` 声明一个类，不过 Kotlin 里面如果类实体没有什么内容的话，可以不要大括号。

**Swift:**

哦哦，不要大括号，那好简洁啊，我们不行。不过我们实例化的时候可以不用 new 关键字。也就是 Swift 程序员不用 new 就可以有对象了😍。

**Kotlin:**

嘿，正经点，你都是有老婆的人了。不过这个我们也有😜，我们 Kotlin 程序员也可以不 new 就有对象了😄。

Kotlin 的构造函数分为主构造函数和次构造函数。主构造函数是和类名称一起写在类头部，次构造函数是写在类体里面的。它们都是用 `constructor` 修饰。不过在定义主构造函数时，如果没有注解什么的，就可以不写了，所以看起来还是很👍的。

```kotlin
class Person constructor(firstName: String) {
}

class Person(firstName: String)
```

**Swift:**

🤔，和 Swift 的概念差不过，我们叫「指定初始化器」（Designated Initializer）和「便捷初始化器」（Convenience Initializer）。都是写在类里面的，指定初始化器用 `init` 修饰，便捷初始化器需要再加个 `convenience` 关键字。

```swift
class SomeClass {
    private let string: String
    
    init(string: String) {
        self.string = string
    }
    
    convenience init() {
        self.init(string: "KotlinThree")
    }
    
    func printString() {
        print(string)
    }
}
```

哎，你们这样主构造函数的初始化代码写在哪里。

**Kotlin:**

👍，这个问题问的太好了，我们主构造函数不能写代码😂。不过 Kotlin 提供了以 init 为关键字的初始化块用来写初始化代码，以解决主构造函数不能写代码的问题。

```kotlin
class Customer(name: String) {
    init {
        logger.info("Customer initialized with value ${name}")
    }
}
```

Kotlin 的次构造函数必须直接或间接（通过其他次构造函数）委托给主构造函数，委托到同一个类的另一个构造函数用 this 关键字，你们应该也有这个限制吧。

**Swift:**

是的，Swift 便捷初始化器需要去调用指定初始化器来完成初始化。听说你们可以用构造函数的参数定义类的属性。

**Kotlin:**

哟，🙆。Kotlin 可以通过在主构造函数参数前面添加 val 或者 var 修饰符，这样主构造函数的参数就变成了类的属性，这样就不需要再在类里面定义同样的属性再赋值了。

**Swift:**

666，👏。

Swift 有类方法和实例方法，用 class 或 static 关键字修饰的方法就是类方法，这两个关键字的区别是 class 修饰的类方法可以被子类复写，static 修饰的类方法不行。既没用 class 修饰，也没用 static 修饰的就是实例方法。

**Kotlin:**

Kotlin 里面已经没有类方法的概念了。🤔不过，Kotlin 中可以用 object 关键字直接定义一个对象，在类内部，我们可以用 companion 为类声明一个伴生对象。伴生对象的成员可通过只使用类名作为限定符来调用，伴生对象的成员看起来像 Java 的静态成员，在运行时他们仍然是真实对象的实例成员。在 JVM 平台，如果使用 @JvmStatic 注解，你可以将伴生对象的成员生成为真正的静态方法和字段。

不过你们的类方法还可以被子类重写，这个在 Java 里也不行。诶，说说你们类是怎么继承的啊。

**Swift**

Swift 中用 `:` 来声明类的继承关系，你们也是用冒号来继承一个类吧？

**Kotlin:**

是的，再也不用区分 `extends` 还是 `implements` 了。在 Kotlin 里面，所有的非抽象类默认都是静态的，也就是相当于 Java 中的 final。如果想要让某个类可以被继承，必须要现式的为该类添加 open 的关键字，该关键字提供了和 Java 中 final 相反的功能。

**Swift:**

😯，为什么要区分？

**Kotlin:**

因为在 Java 继承类和实现接口使用不同的关键字。

**Swift:**

Swift 中继承类和实现协议也都是用同一个 `:` 符号。Swift 里面如果不想让一个类能被继承，可以在声明类时加上 final 关键字。另外如果两个类分辨属于不同的模块，基类必须用 open 关键字修饰才能被另一个模块的类继承。

**Kotlin:**

那你们有没有抽象类的概念啊。

**Swift:**

👋没有。

**Kotlin:**

Kotlin 不但类默认是静态的，函数也是静态的，如果一个函数需要被重写，我们必须手动让他变成开放的，即在函数前面添加 open 关键字。如果子类想要重写某个方法，必须用 override 关键字修饰该方法，否则会报错。被 override 修饰的函数默认也是开放的，如果不想它再被继承，需要 final 来修饰该函数。

**Swift:**

Swift 函数倒是不需要，不过也需要用 override 关键字来修饰。构造函数的覆盖也是一样的，子类覆盖父类初始化器的步骤：

- 初始化子类的所有成员变量
- 用 super 调用父类的初始化器
- 一些额外的操作

```swift
class SomeClass: BaseClass {
    let text: String

    override init() {
        text = "abc"
        super.init()
        loadData()
    }
    ...
}
```

**Kotlin:**

Kotlin 如果有主构造函数的话，是直接在父类名称后面传递对应的参数。如果类没有主构造函数，那么每个次构造函数必须 使用 super 关键字初始化其基类型，或委托给另一个构造函数做到这一点。 注意，在这种情况下，不同的次构造函数可以调用基类型的不同的构造函数：

```kotlin
class Derived(p: Int) : Base(p)

class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```

Kotlin 属性覆盖和函数覆盖类似，需要使用 open 和 override 关键字，覆盖属性的类型必须兼容。可以用 var 属性覆盖 val 属性，反过来却不行。这是因为 var 本质上是多声明了一个 setter 方法。

**Swift:**

Swift 中储值属性不能覆盖，只能覆盖父类的计算属性，同样需要加上 override 关键字，不过属性覆盖用的不是很多。

**Kotlin:**

Swift 如果实现多个接口，会不会有不同协议带来同名函数的冲突的问题。😏

**Swift:**

Swift 如果有同样的名字 IDE 会报错，所以不同的协议如果被同一个类实现不能用同样的名字。😔

**Kotlin:**

😎Kotlin 可以，Kotlin 有一套规则来处理这样的冲突。在 Kotlin 中，如果一个类从它的直接超类继承相同成员的多个实现（由于接口函数可以有实现），它必须覆盖这个成员并提供其自己的实现。 为了表示采用从哪个超类型继承的实现，我们使用由尖括号中超类型名限定的 super，如 super。

```kotlin
open class A {
    open fun f() { print("A") }
    fun a() { print("a") }
}

interface B {
    fun f() { print("B") } // interface members are 'open' by default
    fun b() { print("b") }
}

class C() : A(), B {
    // The compiler requires f() to be overridden:
    override fun f() {
        super<A>.f() // call to A.f()
        super<B>.f() // call to B.f()
    }
}
```

**Swift:**

这个不错，可以不怕名字冲突了。给你们手动点个赞 👍。

**Kotlin:**

😳，Kotlin 里面还新增了嵌套类的概念，就是可以直接在类体里面另外一个类，其实就是之前 Java 里面的静态内部类。这种写法在 Java 里面就是定义内部类，在 Kotlin 里面要定义内部类反而要加上 `Inner` 关键字。

**Swift:**

Swift 没有内部类的概念。😓

**Kotlin:**

🤗你们没有抽象类，也没有内部类，不过你们的协议好像有很多玩法。下次听你给我好好讲讲你们的协议是怎么样的？

**Swift:**

🤔好的，没有问题。


# 技术知识

## Kotlin

### 类的定义

Kotlin 使用 `class` 关键字声明类。

```kotlin
class A {
}
```

类声明由类名称、类头（指定其类型参数、主 构造函数等）和由大括号包围的类体构成。类头和类体都是可选的； 如果一个类没有类体，可以省略花括号。

```kotlin
class A
```

### 实例化

但我们想要实例化一个类的对象的时候，不需要提供 `new` 关键字。

```kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

### 构造函数

Kotlin 的构造函数分为 **主构造函数** 和 **次构造函数**，主构造函数只能有一个，次构造函数可以有多个。

#### 主构造函数

主构造函数是类头的一部分：它跟在类名（和可选的类型参数）后，用 `constructor ` 关键字表示。

```kotlin
class Person constructor(firstName: String) {
}
```

当主构造函数没有任何注解或者可见性修饰符，可以省略这个 `constructor ` 关键字。

```kotlin
class Person(firstName: String)
```

主构造函数没有自己的函数块，所以不能写任何代码，Kotlin 提供了以 `init` 为关键字的初始化块用来写初始化代码，以解决主构造函数不能写代码的问题，所以在初始化块中可以随意访问主构造函数的参数。

```kotlin
class Customer(name: String) {
    init {
        logger.info("Customer initialized with value ${name}")
    }
}
```

默认情况下主构造函数的参数只能被 `初始化块` 和 `属性初始化` 访问。

```kotlin
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

不过我们可以通过在主构造函数参数前面添加 `val` 或者 `var` 修饰符，这样朱构造函数的参数就变成了类的属性。

```kotlin
class Person(val firstName: String, val lastName: String, var age: Int) {
    // ……
}
```

**PS:如果构造函数有注解或可见性修饰符，这个 constructor 关键字是必需的，并且 这些修饰符在它前面**

#### 次构造函数

次构造函数也是用 `constructor` 修饰，写在类体里面，可以有多个。

```kotlin
class Person {
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

如果类有主构造函数的话，次构造函数必须直接或间接（通过其他次构造函数）委托给主构造函数，委托到同一个类的另一个构造函数用 `this ` 关键字。

```kotlin
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

如果一个非抽象类没有声明任何（主或次）构造函数，它会有一个生成的 不带参数的主构造函数。构造函数的可见性是 public。如果你不希望你的类 有一个公有构造函数，你需要声明一个带有非默认可见性的空的主构造函数。

```kotlin
class DontCreateMe private constructor () {
}
```

### 类的成员

Kotlin 类可以包含下面这些

- 构造函数和初始化块
- 函数
- 属性
- 嵌套类和内部类
- 对象声明

### 继承

我们用 `:` 声明要继承一个超级类，放在冒号后面。

```kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

如果类没有明确声明一个父类的话，默认是继承自 `Any`，`Any` 并不是 java.lang.Object；它除了 equals()、hashCode()和toString()外没有任何成员。 更多细节请查阅[Java互操作性](https://kotlinlang.org/docs/reference/java-interop.html#object-methods)部分。

在 Kotlin 里面，所有的非抽象类默认都是静态的，也就是相当于 Java 中的 final。如果想要让某个类可以被继承，必须要现式的为该类添加 `open` 的关键字，该关键字提供了和 Java 中 final 相反的功能。

如果类没有主构造函数，那么每个次构造函数必须 使用 super 关键字初始化其基类型，或委托给另一个构造函数做到这一点。 注意，在这种情况下，不同的次构造函数可以调用基类型的不同的构造函数：

```kotlin
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```

#### 抽象类与接口

同 Java 一样，Kotlin 用 `abstract` 声明一个抽象类，用 `interface` 关键字来定义接口，与 Java8 相似，接口中可以有函数的实现。

与 Java 不同，你可以在接口中定义属性。在接口中声明的属性要么是抽象的，要么提供 访问器的实现。在接口中声明的属性不能有幕后字段（backing field），因此接口中声明的访问器 不能引用它们。

```kotlin
interface MyInterface {
    val prop: Int // 抽象的

    val propertyWithImplementation: String
        get() = "foo"

    fun foo() {
        print(prop)
    }
}

class Child : MyInterface {
    override val prop: Int = 29
}
```

#### 覆盖方法

默认类的函数也是静态的，如果一个函数需要被重写，我们必须手动让他变成开放的，即在函数前面添加 `open` 关键字。如果子类想要重写某个方法，必须用 `override` 关键字修饰该方法，否则会报错。

```kotlin
open class Base {
    open fun v() {}
    fun nv() {}
}
class Derived() : Base() {
    override fun v() {}
}
```

被 `override` 修饰的函数默认也是开放的，如果不想它再被继承，需要 `final` 来修饰该函数。

```kotlin
open class AnotherDerived() : Base() {
    final override fun v() {}
}
```

#### 覆盖属性

属性覆盖和函数覆盖类似，需要使用 `open` 和 `override` 关键字，覆盖属性的类型必须兼容。

```
open class Foo {
    open val x: Int get { ... }
}

class Bar1 : Foo() {
    override val x: Int = ...
}
```

我们可以用 `var` 属性覆盖 `val` 属性，反过来却不行。这是因为 `var` 本质上是多声明了一个 `setter` 方法。

此外，我们还可以在类的主构造函数的参数也可以同样实现属性覆盖。

```kotlin
interface Foo {
    val count: Int
}

class Bar1(override val count: Int) : Foo

class Bar2 : Foo {
    override var count: Int = 0
}
```

#### 覆盖规则

在 Kotlin 中，如果一个类从它的直接超类继承相同成员的多个实现（由于接口函数可以有实现），它必须覆盖这个成员并提供其自己的实现。 为了表示采用从哪个超类型继承的实现，我们使用由尖括号中超类型名限定的 super，如 super<Base>。

```kotlin
open class A {
    open fun f() { print("A") }
    fun a() { print("a") }
}

interface B {
    fun f() { print("B") } // interface members are 'open' by default
    fun b() { print("b") }
}

class C() : A(), B {
    // The compiler requires f() to be overridden:
    override fun f() {
        super<A>.f() // call to A.f()
        super<B>.f() // call to B.f()
    }
}
```

同时继承 A 和 B 没问题，并且 a() 和 b() 也没问题因为 C 只继承了每个函数的一个实现。 但是 f() 由 C 继承了两个实现，所以我们必须在 C 中覆盖 f() 并且提供我们自己的实现来消除歧义。

### 伴生对象

Kotlin 中可以用 `object` 关键字直接定义一个对象，在类内部，我们可以用 `companion` 为类声明一个伴生对象。伴生对象的成员可通过只使用类名作为限定符来调用，伴生对象的成员看起来像 Java 的静态成员，在运行时他们仍然是真实对象的实例成员。在 JVM 平台，如果使用 @JvmStatic 注解，你可以将伴生对象的成员生成为真正的 静态方法和字段。更详细信息请参见[Java 互操作](https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html#static-fields)性一节。

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}

val instance = MyClass.create()
```

### 内部类和嵌套类

我们可以直接在一个类里面定义另外一个类。当里面的类被 `Inner` 修饰符修饰，则是类的内部类，否则我们只是定义了一个嵌套类。

**嵌套类：**

```kotlin
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```

**内部类：**

```kotlin
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```

在 Kotlin 也有匿名内部类的概念，不过使用 `object` 表达式修饰：

```kotlin
window.addMouseListener(object: MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }
                                                                                                            
    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
```

## Swift

### 类的定义

Swift 中用 `class` 关键字来定义类：

```swift
class SomeClass {

}
```


然后可以用以下方式创建这个类的实例：

```swift
let instance = SomeClass()
```

**PS:  Swift 中没有 NSObject 的基类概念**

### 构造函数（初始化器）

构造函数也可以叫做初始化器（Initializer）  
用 `init` 关键字来定义类的构造函数

```swift
class SomeClass {
    init(string: String) {
	
    }
}

let instance = SomeClass(string: "KotlinThree")
```

如果类含有成员变量，在类初始化时，必须保证所有成员变量都被初始化。
对于 Optional 类型的成员变量，如果没有显式地初始化，编译器会自动把它初始化为 nil。对于非 Optional 类型的成员变量，必须显式地初始化。

```swift
class SomeClass {
    let number: Int
    let defaultValueNumber = 3
    let optionalString: String?

    init() {
        number = 1
    }
}
```


### 析构函数

用 `deinit` 关键字来定义析构函数，析构函数在类销毁时调用。

```swift
class SomeClass {
    deinit {
	
    }
}
```

### 指定初始化器和便捷初始化器
 
指定初始化器（Designated Initializer）是类的主要初始化器，每个类都至少需要有一个指定初始化器。

在上面的例子中用 init 定义的就是指定初始化器。

便捷初始化器（Convenience Initializer）需要用 `convenience` 来修饰。便捷初始化器需要去调用指定初始化器来完成初始化。

```swift
class SomeClass {
    private let string: String
    
    init(string: String) {
        self.string = string
    }
    
    convenience init() {
        self.init(string: "KotlinThree")
    }
    
    func printString() {
        print(string)
    }
}

let instance = SomeClass()
instance.printString()
/*
输出结果：
KotlinThree
*/
```

### 类方法和实例方法

用 `class` 或 `static` 关键字修饰的方法就是类方法，这两个关键字的区别是 `class` 修饰的类方法可以被子类复写，`static` 修饰的类方法不行。

既没用 `class` 修饰，也没用 `static` 修饰的就是实例方法。

```swift
class SomeClass {
    class func classMethod() {
    
    }
	
    static func cannotBeOverridedClassMethod() {
	
    }
	
    func instanceMethod() {
	
    }
}

// 调用类方法
SomeClass.classMethod()
// 调用实例方法
let instance = SomeClass()
instance. instanceMethod()
```

### 类的继承

Swift 中用 `:` 来声明类的继承关系

```swift
class BaseClass {
    func baseFunction() {
	
    }
}

class SomeClass: BaseClass {

}
```

子类会获得父类的非 private 的属性和方法

```swift
let instance = SomeClass()
instance.baseFunction()
```

如果不想让一个类能被继承，可以在声明类时加上 `final` 关键字

```swift
final class BaseClass {}
```

另外如果两个类分辨属于不同的模块，基类必须用 `open` 关键字修饰才能被另一个模块的类继承。

**PS: Swift3 提供了五个访问控制的关键字 `open``public` `private` `fileprivate` `internal` 
用来控制访问级别，`internal`表示在模块内部可以完全访问，在模块外部不行，`fileprivate` 限定只能在当前的源文件中使用** 


### 必要初始化器

必要初始化器（Required Initializers）使用 `required` 来修饰表明，如果子类继承父类都必须实现该初始化器。如果不使用指定初始化器，则无需显示写出。

```
class BaseClass {
    
    public var string: String
    required init() {
        string = "string"
    }
    
}

class SomeClass: BaseClass {
    
    required init() {} //如不实现则报错， 如不创建指定初始化器则可以省略
    
    init(label: String) {
        super.init()
    }
}

```

### 方法覆盖

子类如果需要复写父类的方法，需要用 `override` 关键字来修饰

```swift
class SomeClass: BaseClass {
    override func baseFunction() {
        // ...
    }
}
```

子类方法调用父类的相同方法，用 `super` 关键字，例如：

```swift
class SomeClass: BaseClass {
    override func baseFunction() {
        super.baseFunction()
        // ...
    }
}
```

覆盖父类初始化器的步骤：

1. 初始化子类的所有成员变量
2. 用 super 调用父类的初始化器
3. 一些额外的操作

```swift
class SomeClass: BaseClass {
    let text: String

    override init() {
        text = "abc"
        super.init()
        loadData()
    }
}
```

### 类的嵌套

Swift 中可以在类中再定义一个类

```
class SomeClass {

    class AnotherClass {

    }
}
```

可以用 `.` 号的方式来在外面实例化里面嵌套的类

```swift
let instance = SomeClass.AnotherClass()
```

也可以给 AnotherClass 加上 `private`，这样就无法再外面实例化了，只能在 SomeClass 这个类中实例化。

```swift
class SomeClass {

    private class AnotherClass {

    }

    private let instance = AnotherClass() // 可以编译通过
}

let instance = SomeClass.AnotherClass() // 无法编译通过
```

## 关于《Swift vs. Kotlin 漫谈》系列

- 《Swift vs. Kotlin 漫谈》系列之变量定义
- 《Swift vs. Kotlin 漫谈》系列之函数定义
- 《Swift vs. Kotlin 漫谈》系列之控制流
- 《Swift vs. Kotlin 漫谈》系列之基本类型

《Swift vs. Kotlin 漫谈》系列是由 KotlinThree 发起的，旨在把 Swift 和 Kotlin 进行一个全面的对比，帮助 iOS 和 Android 开发对彼此的语言之间有一个更加深入的认识。如果你有兴趣可以关注我们公众号，也可以通过「原文链接」查看详情。

---
![](http://7xpox6.com1.z0.glb.clouddn.com/kotlin-three-wechat.jpg)