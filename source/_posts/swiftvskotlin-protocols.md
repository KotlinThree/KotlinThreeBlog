title: Swift vs. Kotlin 漫谈之函数定义
date: 2017-05-25 13:07:40
tags: 
- Swift
- Kotlin
- swift vs kotlin


Kotlin 君和 Swift 君在一个团队一起开发已经很久了，由于平台的差异性，他们经常会进行一些技术上的交流（PK），「Kotlin vs. Swift」课程就是他们在互相切磋是的语录。内容会由简及深，慢慢深入。

<!--more-->

# 技术漫谈

**Kotlin:**

Hi, 小 S, Swift 4 发布了，有啥新变化吗？

**Swift:**

哈，小 K, 你也关注 WWDC 啊。

总体来说 Swift 4 没有从 Swift 2 到 Swift 3 变化那么大，但也有很多强大的新特性值得我们去学习，我们的土豪铎已经总结好了 Swift 4 的所有新特性，可以直接点击链接去学习。[最全的 Swift 4 新特性解析](http://liuduo.me/2017/06/09/Whats_new_in_swift_4_completely/)

**Kotlin:**

👍，真有速度。

**Swift:**

😆，必须滴，我们今天就基于 Swift 4 的新语法来讨论一下接口吧？

**Kotlin:**

好啊，接口对我们开发来说是个很重要的概念。设计模式中要求我们写代码要遵循依赖倒置原则，就是程序要依赖于抽象接口，不要依赖于具体实现，也就是要求我们要面向接口编程。

**Swift:**

是的，在 Swift 中，接口被称为协议（即 `Protocol` ）, 苹果大大强化了 `Protocol` 在这门语言中的地位，整个 Swift 标准库也是基于 `Protocol` 来设计的，可以说 Swift 是一门面向 `protocol` 编程的语言。

**Kotlin:**

听起来好流比，那来说说你们是怎么定义接口的？

**Swift:**

我们用 `Protocol` 关键字来定义接口：

```swift
protocol SomeProtocol {
    func f()
}
```
你们呢？

**Kotlin:**

我们同 Java 一样，用 `interface` 关键字来定义接口：

```kotlin
interface MyInterface {
    fun f()
}
```
**Swift:**

嗯，看起来就是关键字不一样。你们怎么实现接口呢？

**Kotlin:**

一个类要实现某个接口，需要在类型名称后加上协议名称，中间以冒号（`:`）分隔：

```kotlin
class MyClass: MyInterface {
    override fun f() {
       // 具体实现
    }
}
```
一个类或者对象可以实现一个或多个接口。实现多个接口时，各接口之间用逗号（`,`）分隔.

**Swift:**

我们也是一样的，只是我们不需要写 `override` 关键字，只有当子类复写父类的方法或计算属性时才需要用 `override` 修饰。另外，我们还可以通过扩展类型来实现协议：

```swift
class MyClass {
    //...类的定义
}

extension MyClass: SomeProtocol {
    func f() {
        // 具体实现
    }
}
```
**Kotlin:**

👍 ，这意味着你们不用修改原有类型，就可以让原有类型符合某个协议了，甚至可以扩展标准库中的某个基础类型来实现自定义的协议。这很符合开闭原则嘛。

**Swift:**

是啊，牛不牛 😆。

我们实现协议的类型除了 `class` 外，还可以是 `struct` 或 `enum`。

**Kotlin:**

Kotlin 没有结构体的概念， `enum` 也可以实现接口。

来说说你们的接口中可以声明哪些东西吧？

**Swift:**

我们可以在协议中声明属性和方法，用 `var` 关键字来声明变量属性，并在属性声明后加上 `{ set get }` 来表示属性是可读可写的，用 `{ get }` 来表示属性是只读的。

协议里面声明的属性和方法一定是抽象的，不能有实现，由符合协议的类型来提供所有属性和方法的实现。

**Kotlin:**

我们也可以声明属性和方法，而且 Kotlin 可以直接在接口中为属性和方法提供默认实现：

```kotlin
interface MyInterface {
    val prop: Int // 抽象的
    val propertyWithImplementation: String
        get() = "foo"

    fun foo() {
        print(prop)
    }
}

class MyClass : MyInterface {
    override val prop: Int = 29
}
```

**Swift:**

👍，虽然我们不能在协议中直接提供属性和方法的默认实现，但是我们可以通过协议扩展来达到此目的。

```swift
protocol MyProtocol {
    var prop: Int { get set }
    var propertyWithImplementation: String { get }
    func foo()
}

extension MyProtocol {
    var propertyWithImplementation: String {
        return "foo"
    }
    
    func foo() {
        print(prop)
    }
}

class MyClass: MyProtocol {
    var prop: Int = 29
}

```

**Kotlin:**

哇~，你们这个协议扩展有点厉害了。

**Swift:**

是的，正是这个特性，才使得我们面向协议编程成为可能。我们甚至可以在扩展中添加协议里没有定义过的方法和属性。

```swift
extension MyProtocol {
    func isExceed() -> Bool {
        return prop > 30
    }
}
let instance = MyClass()
print(instance.isExceed())
// false
```

**Kotlin:**

🙏，这就意味着你们也有能力扩展标准库里的协议了，可以很方便的给标准库里的协议添加新的方法和属性。

**Swift:**

聪明，确实是这样。不仅如此，在扩展协议的时候，还可以指定一些限制条件，只有遵循协议的类型满足这些限制条件时，才能获得协议扩展提供的默认实现。

```swift
protocol TextRepresentable {
    var textualDescription: String { get }
}

struct Hamster: TextRepresentable {
    var name: String
    var textualDescription: String {
        return "A hamster named \(name)"
    }
}

extension Collection where Iterator.Element: TextRepresentable {
    var textualDescription: String {
        let itemsAsText = self.map { $0.textualDescription }
        return "[" + itemsAsText.joined(separator: ", ") + "]"
    }
}

let hamsters = [Hamster(name: "Jim"), Hamster(name: "Merry")]
print(hamsters.textualDescription)
// [A hamster named Jim, A hamster named Merry]
```
这里扩展了 Swift 标准库中的 `Collection` 协议，但是限制只适用于集合中的元素遵循了 `TextRepresentable` 协议的情况。 因为 `Swift` 中 `Array` 符合 `Collection` 协议，而 `Hamster` 类型又符合 `TextRepresentable` 协议，所以 `hamsters` 可以使用 `textualDescription` 属性得到数组内容的文本表示。

**Kotlin:**

赞啊~，你们这个协议扩展太强大了，不仅可以扩展自己定义的协议，还可以扩展标准库中的协议，怪不得苹果称 `Swift` 是面向协议编程的语言。

Swift 在实现多个协议时，会不会有不同协议带来同名方法或属性的冲突的问题？

**Swift:**

我们还不能很好地处理多个协议的冲突问题。😭

**Kotlin:**

😎 Kotlin 可以，Kotlin 有一套规则来处理这样的冲突。在 Kotlin 中，如果一个类从它的直接超类继承相同成员的多个实现（由于接口函数可以有实现），它必须覆盖这个成员并提供其自己的实现。 为了表示采用从哪个超类型继承的实现，我们使用由尖括号中超类型名限定的 super，如 super<A>。

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

这个好赞，可以不怕名字冲突了 👍。

Kotlin 的接口中可以声明类方法吗？

**Kotlin:**

Kotlin 里面已经没有类方法的概念了。

**Swift:**

我们可以在协议中使用 `static` 关键字来声明类型方法，如果实现该协议的类型是 `class` 类型，则在实现类中除了用 `static` 来修饰类型方法外，也可以使用 `class`关键字.

```swift
protocol SomeProtocol {
    static func someTypeMethod()
}

class SomeClass: SomeProtocol {
    // 这里也可以用 static 修饰，区别是 static 修饰的属性
    // 或方法不能被子类复写，class 修饰的可以被子类复写
    class func someTypeMethod() {
        print("type method")
    }
}
```
**Kotlin:**

我们的接口虽然不支持类方法，但是我们可以给接口中定义的方法的参数设置默认值。

**Swift:**

这。。。我们不支持为协议中的方法的参数提供默认值。😭

**Kotlin:**

😏，方法参数的默认值必须定义在接口中，在实现类或对象实现该方法时，不能为函数提供默认值。同时接口的中函数不能用 `JVMOverride` 注解修饰，所以接口中定义的带有默认值的参数，不能为 Java 生成重载方法，如果接口是定义在库里面，Kotlin 的实现也无法使用自动重载功能，需要手动重载。

```kotlin
interface IDownload{
	fun(url: String, isSupportBreakpointC: Boolean = true)
}

class DownloadImpl: IDownload{
	override fun(url: String, isSupportBreakpointC: Boolean){
		
	}
}
```

**Swift:**

🐂，这点算你强。

我们的协议中可以定义可变方法，如果协议中定义的实例方法会改变遵循该协议的类型的实例，那么需要在该方法前加 `mutating` 关键字, 表示可以在该方法中修改它所属的实例以及实例的任意属性的值, 例如：

```swift
protocol Togglable {
    mutating func toggle()
}

enum OnOffSwitch: Togglable {
    case off, on
    mutating func toggle() {
        switch self {
        case .off:
            self = .on
        case .on:
            self = .off
        }
    }
}
var lightSwitch = OnOffSwitch.off
lightSwitch.toggle()
// lightSwitch 现在的值为 .On
```

**Kotlin:**

🙄 我们没这特性，这点你赢了。

**Swift:**

岂止如此，我们的协议中还可以要求遵循协议的类型实现指定的构造器：

```swift
protocol SomeProtocol {
    init(someParameter: Int)
}

class SomeClass: SomeProtocol {
    required init(someParameter: Int) {
        // initializer implementation goes here
    }
}
```
在符合协议的类中实现构造器，必须在构造器实现前加上 `required` 修饰符。使用 `required` 修饰符可以确保所有子类也必须提供此构造器实现，从而也能符合协议。 如果类已经被标记为 `final`，那么不需要在协议构造器的实现中使用 `required` 修饰符，因为 `final` 类不能有子类.

协议还可以为遵循协议的类型定义可失败构造器。

**Kotlin:**

好吧，我们不可以在接口中声明构造器。

**Swift:**

😄，你们的接口可以继承吗？

Swift 中协议能够继承一个或多个其他协议，可以在继承的协议的基础上增加新的要求.

**Kotlin:**

当然可以，这是基本的用法好伐。

**Swift:**

好吧。。我们还可以通过让协议继承 `AnyObject` 协议来限制协议只能被 `Class` 类型遵循，而结构体或枚举不能遵循该协议。

**Kotlin:**

我们并没有这种限制，接口可以被类和枚举实现。

**Swift:**

你们的接口可以组合吗？

Swift 可以采用 `&` 符号将多个协议进行组合：

```swift
protocol Named {
    var name: String { get }
}
protocol Aged {
    var age: Int { get }
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}
func wishHappyBirthday(to celebrator: Named & Aged) {
    print("Happy birthday, \(celebrator.name), you're \(celebrator.age)!")
}
let birthdayPerson = Person(name: "Malcolm", age: 21)
wishHappyBirthday(to: birthdayPerson)
// Prints "Happy birthday, Malcolm, you're 21!"
```
这里 `wishHappyBirthday(to:)` 函数的参数类型为 `Named & Aged`, 这意味着它不关心参数的具体类型，只要参数符合这两个协议即可。当然也可以给组合的协议指定一个别名：`typealias Property = Named & Aged`

**Kotlin:**

666，你们的协议真是玩出花了，这个功能我们也没有😢。

**Swift:** 

除了协议与协议组合外，协议还可以与类进行组合：

```swift
class Location {
    var latitude: Double
    var longitude: Double
    init(latitude: Double, longitude: Double) {
        self.latitude = latitude
        self.longitude = longitude
    }
}
class City: Location, Named {
    var name: String
    init(name: String, latitude: Double, longitude: Double) {
        self.name = name
        super.init(latitude: latitude, longitude: longitude)
    }
}
func beginConcert(in location: Location & Named) {
    print("Hello, \(location.name)!")
}
 
let seattle = City(name: "Seattle", latitude: 47.6, longitude: -122.3)
beginConcert(in: seattle)
```
这里的 `beginConcert(in:)` 函数的参数要求是 `Location` 的子类，且必须符合 `Named` 协议.

**Kotlin:**

太赞了~，给你点32个赞👍！

**Swift:**

你们是怎么判断某个实例是否符合某个协议的？

**Kotlin:**

这就是判断某个对象是否是某个类型嘛，当然是用 `is` 啦，如果是类型转换的话用 `as` 。

**Swift:**

嗯嗯，这点我们是一致的。

你们可以定义接口中的方法或属性为可选吗？

**Kotlin:**

何谓可选？

**Swift:**

就是可以实现也可以不实现

**Kotlin:**

前面讲过了啊，如果接口中的属性或方法在实现类中可以实现也可以不实现，则可以在接口定义中为该方法提供默认实现。

**Swift:**

嗯，Swift 是通过协议扩展提供默认实现来到达可选的目的。

不过 Swift 也可以像 `Objective-C` 里那样定义可选的接口方法，就需要在 `protocol` 定义之前加上 `@objc`，将 `protocol` 变为 `Objective-C` 的。然后使用 `optional` 关键字来声明某些方法或属性在符合该协议的类中可以不实现，如下：

```
@objc protocol CounterDataSource {
    @objc optional func incrementForCount(count: Int) -> Int
    @objc optional var fixedIncrement: Int { get }
}
```
需要注意的是，标记为 `@objc` 的 `protocol` 只能被 `class` 实现，不能被 `struct` 和 `enum` 类型实现，而且实现它的 `class` 中的方法也必须被标注为 `@objc`，或者整个类就是继承自 `NSObject`。

**Kotlin:**

额。。。这岂不是很蛋疼

**Swift:**

😆，是的，所以这种方式并不提倡。

`Swift` 可以使用 `Protocol` 来实现委托（代理）模式，委托（代理）模式允许类或结构体将一些需要它们负责的功能委托给其他类型的实例，如下：

```
protocol RentHouserDelegate{
    func rent(_ name:String);
}

class Tenant {
    var name = "lucy"
    var delegate: RentHouserDelegate?
    func rentHouse(){
        delegate?.rent(name)
    }
}

class Intermediary: RentHouserDelegate {
    var name="lily"
    func rent(_ name:String) {
        print("\(name) 请 \(self.name) 帮她租一套房子");
    }
}

var person = Tenant();
person.delegate = Intermediary()
person.rentHouse()
// lucy 请 lily 帮她租一套房子
```
**Kotlin:**

这是接口的一种常用方法，我们依赖注入框架就大量使用这种方式。

**Swift:**

哈哈，英雄所见略同。

好了，就到这吧，今天的PK互有攻防，好带劲😎~

**Kotlin:**

😀，总体来说还是你们的协议比较强大。

**Swift:**

那是，要不然苹果怎么敢称 Swift 是一门面向协议编程的语言呢

**Kotlin:**

好吧，咱们来日方长。

**Swift:**

嗯嗯，后会有期。


## Kotlin

### 接口定义

同 Java 一样，Kotlin 用 `interface` 关键字来定义接口，Kotlin 接口中可以有函数的实现，也可以只有抽象方法，接口无法保存状态，它可以有属性但必须声明为抽象或提供访问器实现。

```kotlin
interface MyInterface {
    fun bar()
    fun foo() {
      // 可选的方法体
    }
}
```

### 实现接口

Kotlin 的一个类或者对象可以实现一个或多个接口。由于 Kotlin 接口本身的函数式可以有实现的，所以在一个类或对象实现多个接口的时候，就有可能发生冲突，这包括接口之间的的成员冲突，也包括接口与父类直接的成员冲突。

#### 覆盖冲突

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

### 接口中的属性

Kotlin 中可以在接口中定义属性。在接口中声明的属性要么是抽象的，要么提供访问器的实现。在接口中声明的属性不能有幕后字段（backing field），因此接口中声明的访问器不能引用它们。

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

### 函数默认参数与重载

如果接口函数需要定义默认值的话，必须定义在接口中，在实现类或对象实现该方法时，不能为函数提供默认值。同时接口的中函数不能用 `JVMOverride` 注解修饰，所以接口中定义的带有默认值的参数，不能为 Java 生成重载方法，如果接口是定义在库里面，Kotlin 的实现也无法使用自动重载功能，需要手动重载。

```
interface IDownload{
	fun(url: String, isSupportBreakpointC: Boolean = true)
}

class DownloadImpl{
	override fun(url: String, isSupportBreakpointC: Boolean){
		
	}
}
```

## Swift

### Protocol

`Swift` 是一门支持面向协议编程的语言，在 `Swift` 语言中，协议被赋予了更多的功能和更广阔的使用空间。恰逢苹果发布了 `swift 4`，以下都是基于最新的 `swift 4` 语法进行讲述。

#### 协议语法

协议声明：

```
protocol SomeProtocol {
    // protocol definition goes here
}
```

要让自定义类型符合某个协议，需要在类型名称后加上协议名称，中间以冒号（`:`）分隔。符合多个协议时，各协议之间用逗号（`,`）分隔. `swift` 中，符合协议的类型可以是 `class`、`struct` 或 `enum`。


```
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // structure definition goes here
}
```
需要注意的是，如果某个类在符合某个协议的同时又继承自某个父类，应将其父类名放在其符合的协议名之前。

#### 协议属性声明

协议中可以声明符合此协议的类型必须实现的属性：

```
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}
```
协议不指定属性是存储型属性还是计算型属性，它只指定属性的名称和类型，以及属性是可读的还是可读可写的。

协议总是用 `var` 关键字来声明变量属性，在类型声明后加上 `{ set get }` 来表示属性是可读可写的，用 `{ get }` 来表示属性是只读的。

协议中总是使用 `static` 关键字定义类型属性，如果是 `class`类型实现协议，除了 `static`，还可以使用 `class` 关键字来声明类型属性。

```
protocol AnotherProtocol {
    static var someTypeProperty: Int { get }
}

class SomeClass: AnotherProtocol {
    // 这里也可以用 static 修饰，区别是 static 修饰的属性
    或方法不能被子类复写，class 修饰的可以被子类复写
    class var someTypeProperty: Int {
        return 0
    }
}
```

#### 协议方法声明

协议可以要求遵循协议的类型实现某些指定的实例方法或类方法。需要注意的是，不支持为协议中的方法的参数提供默认值。

```
protocol RandomNumberGenerator {
    func random() -> Double
}
```
与属性类似，在协议中也使用 `static` 定义类方法，当 `class` 类型实现协议时，可以使用 `class` 关键字来修饰.

如果协议中定义的实例方法会改变遵循该协议的类型的实例，那么需要在该方法前加 `mutating` 关键字, 表示可以在该方法中修改它所属的实例以及实例的任意属性的值, 例如：

```
protocol Togglable {
    mutating func toggle()
}

enum OnOffSwitch: Togglable {
    case off, on
    mutating func toggle() {
        switch self {
        case .off:
            self = .on
        case .on:
            self = .off
        }
    }
}
var lightSwitch = OnOffSwitch.off
lightSwitch.toggle()
// lightSwitch 现在的值为 .On
```

### 协议构造器声明

协议可以要求遵循协议的类型实现指定的构造器：

```
protocol SomeProtocol {
    init(someParameter: Int)
}

class SomeClass: SomeProtocol {
    required init(someParameter: Int) {
        // initializer implementation goes here
    }
}
```
在符合协议的类中实现构造器，必须在构造器实现前加上 `required` 修饰符。使用 `required` 修饰符可以确保所有子类也必须提供此构造器实现，从而也能符合协议. 如果类已经被标记为 `final`，那么不需要在协议构造器的实现中使用 `required` 修饰符，因为 `final` 类不能有子类.

协议还可以为遵循协议的类型定义可失败构造器。

#### 委托（代理）模式

`Swift` 可以使用 `Protocol` 来实现委托（代理）模式，委托（代理）模式允许类或结构体将一些需要它们负责的功能委托给其他类型的实例，如下：

```
protocol RentHouserDelegate{
    func rent(_ name:String);
}

class Tenant {
    var name = "lucy"
    var delegate: RentHouserDelegate?
    func rentHouse(){
        delegate?.rent(name)
    }
}

class Intermediary: RentHouserDelegate {
    var name="lily"
    func rent(_ name:String) {
        print("\(name) 请 \(self.name) 帮她租一套房子");
    }
}

var person = Tenant();
person.delegate = Intermediary()
person.rentHouse()
// lucy 请 lily 帮她租一套房子
```
#### 通过扩展遵循协议

可以通过扩展令已有类型遵循并符合协议：

```
protocol TextRepresentable {
    var textualDescription: String { get }
}

struct Circular {
    var radius: Int
}

extension Circular: TextRepresentable {
    var textualDescription: String {
        return "The circular's radius is \(radius)"
    }
}

let circular = Circular(radius: 2)
print(circular.textualDescription)
// The circular's radius is 2
```
当一个类型已经符合了某个协议中的所有要求，却还没有声明遵循该协议时，可以通过空扩展来使该类型遵循该协议:

```
struct Square {
    var width: Int
    var textualDescription: String {
        return "The square's width is \(width)"
    }
}
extension Square: TextRepresentable {}

let square = Square(width: 3)
let squareTextRepresentable: TextRepresentable = square
print(squareTextRepresentable.textualDescription)
// The square's width is 3
```

#### 协议类型

尽管协议本身并未实现任何功能，但是协议可以被当做一个成熟的类型来使用。
协议类型也可以在数组或者字典这样的集合中使用：

```
let things: [TextRepresentable] = [circular, square]
for thing in things {
    print(thing.textualDescription)
}
```
#### 协议的继承

协议能够继承一个或多个其他协议，可以在继承的协议的基础上增加新的要求：

```
protocol PrettyTextRepresentable: TextRepresentable {
    var prettyTextualDescription: String { get }
}

extension Square: PrettyTextRepresentable {
    var prettyTextualDescription: String {
        var output = textualDescription + ": "
        output += "the area is \(width*width)"
        return output
    }
}

print(square.prettyTextualDescription)
// The square's width is 3: the area is 9
```

### Class 类型专属协议

通过让协议继承 `AnyObject` 协议来限制协议只能被 `Class` 类型遵循，而结构体或枚举不能遵循该协议

```
protocol SomeClassOnlyProtocol: AnyObject, SomeInheritedProtocol {
    // class-only protocol definition goes here
}
```

#### 协议合成

可以采用 `&` 符号将多个协议进行组合：

```
protocol Named {
    var name: String { get }
}
protocol Aged {
    var age: Int { get }
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}
func wishHappyBirthday(to celebrator: Named & Aged) {
    print("Happy birthday, \(celebrator.name), you're \(celebrator.age)!")
}
let birthdayPerson = Person(name: "Malcolm", age: 21)
wishHappyBirthday(to: birthdayPerson)
// Prints "Happy birthday, Malcolm, you're 21!"
```
这里 `wishHappyBirthday(to:)` 函数的参数类型为 `Named & Aged`, 这意味着它不关心参数的具体类型，只要参数符合这两个协议即可。当然也可以给组合的协议指定一个别名：`typealias Property = Named & Aged`

除了协议与协议组合外，协议还可以与 `class` 进行组合：

```
class Location {
    var latitude: Double
    var longitude: Double
    init(latitude: Double, longitude: Double) {
        self.latitude = latitude
        self.longitude = longitude
    }
}
class City: Location, Named {
    var name: String
    init(name: String, latitude: Double, longitude: Double) {
        self.name = name
        super.init(latitude: latitude, longitude: longitude)
    }
}
func beginConcert(in location: Location & Named) {
    print("Hello, \(location.name)!")
}
 
let seattle = City(name: "Seattle", latitude: 47.6, longitude: -122.3)
beginConcert(in: seattle)
```
这里的 `beginConcert(in:)` 函数的参数要求是 `Location` 的子类，且必须符合 `Named` 协议.

#### 检查协议一致性

可以通过 `is` `as?` `as` 来检查某个实例是否符合某个协议：

```
let things: [Any] = [circular, square, "abc"]
for thing in things {
    if let object = thing as? TextRepresentable {
        print(object.textualDescription)
    } else {
        print("It does not conform to TextRepresentable")
    }
}
```

#### 可选协议

原生的 `Swift protocol` 里没有可选项，所有定义的方法都是必须实现的。如果想要像 `Objective-C` 里那样定义可选的接口方法，就需要在 `protocol` 定义之前加上 `@objc`，将 `protocol` 变为 `Objective-C` 的。然后使用 `optional` 关键字来声明某些方法或属性在符合该协议的类中可以不实现，如下：

```
@objc protocol CounterDataSource {
    @objc optional func incrementForCount(count: Int) -> Int
    @objc optional var fixedIncrement: Int { get }
}
```
需要注意的是，标记为 `@objc` 的 `protocol` 只能被 `class` 实现，不能被 `struct` 和 `enum` 类型实现，而且实现它的 `class` 中的方法还必须也被标注为 `@objc`，或者整个类就是继承自 `NSObject`。这还是很蛋疼的。

#### 协议扩展

协议可以通过扩展来为遵循协议的类型提供属性和方法的实现，即使协议中没有声明。这样就无需在每个遵循协议的类型中都重复同样的实现，也无需使用全局函数。

```
protocol TextRepresentable {
    var textualDescription: String { get }
}

extension TextRepresentable {
    func hasDescription() -> Bool {
        return !textualDescription.isEmpty
    }
}
```
还可以通过协议扩展来为协议要求的属性、方法提供默认的实现。这样在遵循这个协议的类型中，可以不用实现这个属性或方法，调用的时候默认调 `extension` 中的实现。这也相当于变相将 `protocol` 中的属性或方法设定为了 ``optional`. 

```
extension TextRepresentable {
    var textualDescription: String {
        return "This is a shape"
    }
}
```
在扩展协议的时候，也可以指定一些限制条件，只有遵循协议的类型满足这些限制条件时，才能获得协议扩展提供的默认实现。

```
extension Collection where Iterator.Element: TextRepresentable {
    var textualDescription: String {
        let itemsAsText = self.map { $0.textualDescription }
        return "[" + itemsAsText.joined(separator: ", ") + "]"
    }
}

let circulars = [Circular(radius: 1), Circular(radius: 2)]
print(circulars.textualDescription)
// [The circular's radius is 1, The circular's radius is 2]
```

这里扩展了 `Collection` 协议，但是限制只适用于集合中的元素遵循了 `TextRepresentable` 协议的情况。 因为 `Swift` 中 `Array` 符合 `Collection` 协议，而 `Circular` 类型又符合 `TextRepresentable` 协议，所以 `circulars` 可以使用 `textualDescription` 属性得到数组内容的文本表示

## 关于《Swift vs. Kotlin 漫谈》系列

- 《Swift vs. Kotlin 漫谈》系列之变量定义
- 《Swift vs. Kotlin 漫谈》系列之函数定义
- 《Swift vs. Kotlin 漫谈》系列之控制流
- 《Swift vs. Kotlin 漫谈》系列之基本类型

《Swift vs. Kotlin 漫谈》系列是由 KotlinThree 发起的，旨在把 Swift 和 Kotlin 进行一个全面的对比，帮助 iOS 和 Android 开发对彼此的语言之间有一个更加深入的认识。如果你有兴趣可以关注我们公众号，也可以通过「原文链接」查看详情。

---
![](http://7xpox6.com1.z0.glb.clouddn.com/kotlin-three-wechat.jpg)
