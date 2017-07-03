title: Swift vs. Kotlin 漫谈系列之接口
date: 2017-05-29 13:07:40
tags: 
- Swift
- Kotlin
- swift vs kotlin


Kotlin 君和 Swift 君在一个团队一起开发已经很久了，由于平台的差异性，他们经常会进行一些技术上的交流（PK），「Kotlin vs. Swift」课程就是他们在互相切磋是的语录。内容会由简及深，慢慢深入。

<!--more-->

# 技术漫谈

**Kotlin:**

Swift 君，多日不见，甚是想念。

**Swift:**

想念想念，咱们今天来说说什么呢？

**Kotlin:**

今天来说说属性。

**Swift:**

好的，你先来。

**Kotlin:**

属性在 Kotlin 中就是定义在类中的变量。

**Swift:**

在 Swift 中，属性可以定义在类、枚举和结构体中。

**Kotlin:**

Kotlin 在类中用 val 定义只读属性，用 var 定义可变属性，类的对象可以通过名称访问类的属性。

**Swift:**

这个 Swift 也比较类似，Swift 中用 let 定义只读属性，用 var 定义可变属性。

**Kotlin:**

Kotlin 定义属性的完整语法为：

```
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```

其中 initializer、getter 和 setter 是并不是必须的。在属性类型可以通过 initializer 推断的情况下，属性类型也可以不声明。

举几个例子：

```
val a: Int = 1  // 直接声明
val b = 2   // `Int` 类型是推断出来的
val c: Int  // 在没有初始化赋值的化，需要定义类型
```

**Swift:**

Swift 定义属性的语法大概为：

```
let|var <propertyName>: [<propertyType>] [= <initialValue>]
```

同 Kotlin 很像，例如：

```
let a: Int = 1
let b = 2
var c: Int
```

**Kotlin:**

Kotlin 的属性可以自定义 Getter 和 Setter，具体方式实在属性定义的下一行写上：

```swift
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // 解析字符串并赋值给其他属性
    }
```

**Swift:**

Swift 总定义属性的 Getter 和 Setter 也是跟在属性的下一行

struct Rect {
    var origin = CGPoint()

    var center: CGPoint {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return CGPoint(x: centerX, y: centerY) 
        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}

**Kotlin:**

也可以省去 Getter 只写 Setter：

```
val isEmpty: Boolean
    get() = this.size == 0
```

**Swift:**

Swift 中如果只写 Getter 就成了只读属性

```
var center: CGPoint {
    get {
        let centerX = origin.x + (size.width / 2)
        let centerY = origin.y + (size.height / 2)
        return CGPoint(x: centerX, y: centerY) 
    }
}
```

**Kotlin:**

Kotlin 可以修改 Setter 方法的可见性，但是只能定义比属性本身相等或者更小的可见性，修改可见性的时候可以只定义可见性关键字和 set 关键字。

```kotlin 
var setterVisibility: String = "abc"
    private set // 此 setter 是私有的并且有默认实现

var setterWithAnnotation: Any? = null
    @Inject set // 用 Inject 注解此 setter
```

**Swift: **

Swift 中大概就是 private(set) 关键字，例如：

```
private(set) var count: Int
```

类似的也有 

```
interval(set) var count: Int
```

**Kotlin:**

Kotlin 中在 Setter 中给自身赋值，是通过 `支持域`，用 `filed` 关键字来表示。

```
var counter = 0 // the initializer value is written directly to the backing field
    set(value) {
        if (value >= 0) field = value
    }
```

**Swift:**

在 Swift 中 Setter 只能定义在计算属性中，而计算属性不需要给自身赋值，没有对应的概念。

**Kotlin:**

Kotlin 中的 Getter，如果想要获取到属性本身的值，需要通过支持属性，支持属性就是将属性通过自定义的访问器完全委托给另外一个相同类型的私有属性。

```kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap() // Type parameters are inferred
        }
        return _table ?: throw AssertionError("Set to null by another thread")
    }
```

**Swift:**

在 Objective-C 中有这种概念，Swift 中的 Setter 只能用在计算属性中，而计算属性时不允许读取自身的值的，因为自身并不保存值。

**Kotlin:**

Kotlin 有个属性委托的概念，可以吧 Getter 和 Setter 交给另一个类来实现。

**Swift:**

Swift 中没有。

**Kotlin:**

下面再说说编译期常亮，已知值的属性可以使用 const 修饰符标记为 编译期常量。

```
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"
```

**Swift:**

Swift 一般把常量定义在类型的外部，例如：

```
private let intConstant = 30

class A {
    // ...
}
```

**Kotlin:**

Kotlin 中的延迟加载有两个关键字 `lateinit` 和 `lazy`  
`lateinit` 用在 var 属性上 
`lazy` 用在 let 属性上

```
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()  // dereference directly
    }
}
```

**Swift:**

Swift 只有一个 `lazy`，只能用在 var 属性上。

```
publpublic class MyTest {
    lazy var subject: TestSubject
}
```

**Kotlin:**

使用 `lateinit` 关键字有一些限制，

- 只能用于在一个类的主体内声明的var属性（不在主构造函数中）
- 该属性没有自定义的getter或setter
- 属性的类型必须为非空，并且不能为原始类型

当访问一个还没有被初始化的延迟加载属性，会抛出相应的异常，告知该属性还未被初始化。

**Swift:**

Swift 的延迟加载属性也只能定义成 var。

**Kotlin:**

官方推荐另外一个委托属性的应用就是 `Observable`，让属性在发生变动的时候可以被关注的地方观察到。

```kotlin
class User {
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}

fun main(args: Array<String>) {
    val user = User()
    user.name = "first"
    user.name = "second"
}
```

**Swift:**

在 Swift 中对应就是 willSet、didSet。

```
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("About to set totalSteps to \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}
```

**Kotlin:**

最后来看看属性覆盖

属性覆盖和函数覆盖类似，需要使用 `open` 和 `override` 关键字，覆盖属性的类型必须兼容。每个声明的属性可以被具有初始化器的属性或具有getter方法的属性覆盖。

```
open class Foo {
    open val x: Int get { ... }
}

class Bar1 : Foo() {
    override val x: Int = ...
}
```

**Swift:**

Swift 中也是这两个关键字：

```
open class Foo {
    open var x: Int
}

class Bar1: Foo {
    override var x: Int
}
```

**Kotlin:**

今天属性就到这里吧，下次见！

**Swift:**

下次见！

# 技术知识

## Kotlin

Kotlin 在类里面用 val 定义只读属性，用 var 定义可变属性。类的对象可以通过名称访问类的属性。

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

### Getters and Setters

我们可以自定义属性的访问器，定义在属性声明内部。

```kotlin
val isEmpty: Boolean
    get() = this.size == 0
```

```kotlin
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // 解析字符串并赋值给其他属性
    }
```

从 1.1 起，Kotlin 还可以根据 Getter 方法推断出属性的类型。

```kotlin
val isEmpty get() = this.size == 0  // 具有类型 Boolean
```

#### 修改可见性和注解
Kotlin 可以修改 Setter 方法的可见性，但是只能定义比属性本身相等或者更小的可见性，修改可见性的时候可以只定义可见性关键字和 set 关键字。

``` Kotlin 
var setterVisibility: String = "abc"
    private set // 此 setter 是私有的并且有默认实现

var setterWithAnnotation: Any? = null
    @Inject set // 用 Inject 注解此 setter
```

### 支持域

Kotlin 类里面并没有域，但是为了自定义访问器 Kotlin 提供了「支持域(Backing Fields)」来达到目的，我们用 `field` 关键字表示。

```kotlin
var counter = 0 // the initializer value is written directly to the backing field
    set(value) {
        if (value >= 0) field = value
    }
```

`field` 关键字只能用在属性的访问器中。

Kotlin 会为属性的默认访问器生成支持域，如果属性的访问器全部被自定义，且在自定义的访问器中没有用到支持域，那么就不会有支持域了。

### 支持属性

支持属性其实就是将属性通过自定义的访问器完全委托给另外一个相同类型的私有属性，我们就将这个私有属性我们就可以称为支持属性。

```kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap() // Type parameters are inferred
        }
        return _table ?: throw AssertionError("Set to null by another thread")
    }
```

### 编译期常量

已知值的属性可以使用 const 修饰符标记为 编译期常量。 这些属性需要满足以下要求：

- 位于顶层或者是 object 的一个成员
- 用 String 或原生类型 值初始化
- 没有自定义 getter

这些属性可以用在注解中：

```
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { ... }
```

### 延迟加载

通常，声明为非空类型的属性必须在构造函数中初始化。但是，很多时候这并不方便。 例如，可以通过依赖注入或单元测试的设置方法初始化属性。在这种情况下，您不能在构造函数中提供非空的初始值，但是在引用类的体内属性时，仍然希望避免空检查。

Kotlin 提供了 `lateinit` 关键字来修饰一个属性，可以让属性支持延迟加载。

```kotlin
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()  // dereference directly
    }
}
```

使用 `lateinit` 关键字有一些限制，

- 只能用于在一个类的主体内声明的var属性（不在主构造函数中）
- 该属性没有自定义的getter或setter
- 属性的类型必须为非空，并且不能为原始类型

当访问一个还没有被初始化的延迟加载属性，会抛出相应的异常，告知该属性还未被初始化。

### 覆盖属性

属性覆盖和函数覆盖类似，需要使用 `open` 和 `override` 关键字，覆盖属性的类型必须兼容。每个声明的属性可以被具有初始化器的属性或具有getter方法的属性覆盖。

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

### 属性委托

定义一个委托属性的语法是 `val/var <property name>: <Type> by <expression>`，其中 `by` 后面的就是属性的委托。属性委托不用继承什么特别的接口，只要拥有用 `operator` 修饰的 `getValue()` 和 `setValue()` (适用 var)的函数就可以了。

```kotlin
class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }
 
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name} in $thisRef.'")
    }
}
```

对于参数的意义：

- `thisRef`，属性的拥有者；
- `property`，对属性的描述，是 KProperty<*> 类型或是它的父类；
- `value`，属性的值。

#### lazy

通过 `lazy` 我们可以定义一个懒加载的属性，该属性的初始化不会再类创建的时候发生，而是在第一次用到它的时候赋值。

```kotlin
val propLazy: Int by lazy{1}
```

#### Observable

官方推荐另外一个委托属性的应用就是 `Observable`，让属性在发生变动的时候可以被关注的地方观察到。

```kotlin
class User {
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}

fun main(args: Array<String>) {
    val user = User()
    user.name = "first"
    user.name = "second"
}
```

#### Storing

`Storing` 的使用场景是被模型的属性全部委托到 `Map` 的结构去真实的存储数据，用于解析 `Json` 或者做一些动态的事情。

```kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
``` 




## Swift

属性可以定义在类、结构体、枚举这三种类型中，用 let 声明只读属性，用 var 声明可变属性，定义属性的基本语法为：

```swift
let|var <propertyName>: [<propertyType>] [= <initialValue>]
```

根据属性本身是否保存值可以分为储值属性和计算属性。

### 储值属性

定义属性时不加 get 和 set 的属性就是储值属性，储值属性本身保存一个值。这个值可以被修改（var），叫可变储值属性；也可以不允许被修改（let），叫不可变储值属性。

例如在结构体 `FixedLengthRange` 中定义两个储值属性：

```swift
struct FixedLengthRange {
    var firstValue: Int
    let length: Int
}
```

可以在创建这个结构体的实例时给它的属性赋初始值

```swift
var rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
```

由于 firstValue 这个属性是用 var 定义的可变属性，可以在实例创建后修改这个属性的值：

```swift
rangeOfFourItems.firstValue = 6
```

而 length 这个属性是用 let 定义的只读属性，它的值不能再被修改。

#### 常量结构体的储值属性

还是上面那个例子，不同的是，如果把实例定义为常量，这时它的所有属性都不能被修改。

```swift
var rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
rangeOfFourItems.firstValue = 6 // 编译报错
```

对于是引用类型的类来说，常量类实例的属性还是可以被修改的。

#### 延迟加载的储值属性

延迟加载的储值属性直到这个属性被第一次使用的时候才会被初始化，用 lazy 关键字来指定一个储值属性是延迟加载的。代码如下：

```
class DataManager {
    lazy var importer = DataImporter()
}
```

注意 lazy 修饰的属性只能用 var 来定义，不能用 let。因为 Swift 的类型的实例在初始化结束后必须保证每个属性都有初始值，而延迟加载的属性再没用到的时候不会执行它的初始化代码来赋值，而是系统会给它一个隐式的初始化值，当属性被使用时，再去执行它的初始化代码给属性设置值，这时相当于改变了属性的值，所以延迟加载只能用在可变属性上。

如果一个类型的属性在程序运行过程中可能从来不会被用到，那么把它定义成延迟加载的是一个不错的选择，可以有效的节省系统资源，提高程序运行效率。

#### 储值属性和实例变量

如果你有较多的 Objective-C 开发经验，那么一定知道在 Objective-C 中，每个属性的背后是一个私有的实例变量在保存属性的值，属性在这个私有实例变量基础上增加了一些额外的 Setter, Getter 操作，提供访问限制，KVO 的支持等。Swift 的属性已经把这两者合为一体，这避免了在不同上下文访问属性值时产生歧义，也简化了属性声明过程。

### 计算属性

在类、结构体和枚举中也可以定义计算属性，计算属性本身并不保存值，它的值通过提供一个 Getter 计算出来，并且可以选择性提供一个 Setter 来进行赋值计算。

#### Setter 和 Getter

```swift
struct Rect {
    var origin = CGPoint()

    var center: CGPoint {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return CGPoint(x: centerX, y: centerY) 
        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}
```

看上面代码，结构体 Rect 中有一个计算属性 center，在这个计算属性定义的后面跟了一堆大括号，里面写了一个 get 和 set。对于计算属性来说，get 是必须的，属性值就是通过 get 中的代码计算得出，get 代码块中的 return 的值就是 center 属性得到的值。

一样也可以提供一个 set 代码块，其中的代码会在的大歌会在给 center 属性赋值时执行，这里在给另一个属性 origin 对象的属性进行赋值。set 关键字后面可以跟一个括号，里面写的是代表给这个属性赋的新值的变量名称，可以随便起，也可以省去这个括号，那么代表新值得变量名默认为 newValue。

因为计算属性的值不是固定的，因此只能用 var 修饰计算属性，不能用 let。

#### 只读计算属性

计算属性可以仅提供 get 代码块，不提供 set 代码块，就像这样：

```swift
var center: CGPoint {
    get {
        let centerX = origin.x + (size.width / 2)
        let centerY = origin.y + (size.height / 2)
        return CGPoint(x: centerX, y: centerY) 
    }
}
```

这是这个 center 为只读计算属性，在这种情况下，可以省去 get 和一对大括号，简写为：

```swift
var center: CGPoint {
    let centerX = origin.x + (size.width / 2)
    let centerY = origin.y + (size.height / 2)
    return CGPoint(x: centerX, y: centerY) 
}
```

### 属性观察

有时候我们想要在属性的值发生变化的之前和之后做一些事情，在 Swift 中，可以很方便的使用属性观察来实现。

在属性定义的时候，Swift 提供了两个关键字：willSet 和 didSet 来引出属性值发生发生变化前，和属性值发生变化后执行的代码块，例如：

```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("About to set totalSteps to \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}
```

看上面代码，StepCounter 类有个 totalSteps 储值属性，在属性定义之后的一对大括号中，写了 willSet 和 didSet 这两个代码块。

当 totalSteps 的值发生变化时，例如：

```swift
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
```

在 stepCounter 的值被实际修改为 200 前，会执行 willSet 中的代码，willSet 后可以跟一个小括号，里面是代表即将付给属性的新值的变量，如果不写小括号，则默认为 newValue。

在 stepCounter 的值被实际修改为 200 之后，会执行 didSet 中的代码，didSet 代码块中，Swift 提供了一个变量 oldValue 代表被修改前的旧值。

### 类型属性

上面所讲的都是实例属性，Swift 中也可以定义类型属性，类型属性属于类型本身，而不属于类型的实例。在属性定义的时候，用 static 修饰的就是类型属性。

```swift
struct SomeStructure {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 1
    }
}
```

上面的 storedTypeProperty 和 computedTypeProperty 就是 SomeStructure 这个类型本身的属性，可以通过：

```swift
SomeStructure.storedTypeProperty
SomeStructure.computedTypeProperty
```

来调用。

对于类，也可以用 class 来修饰，class 允许这个属性被子类复写，这是 class 唯一与 static 不同的地方，static 定义的属性不能被子类复写，例如：

```swift
class SomeClass {
    class var overrideableComputedTypeProperty: Int {
        return 107
    }
}
```





