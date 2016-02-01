title: 类与对象 —— 类（一）
date: 2016-01-02 16:01:28
tags:
- kotlin
- class
- 类
- 继承
- 委托

thumbnail: http://7xpox6.com1.z0.glb.clouddn.com/ski_evening.jpg?imageView2/1/w/200
banner: http://7xpox6.com1.z0.glb.clouddn.com/ski_evening.jpg?imageView2/1/w/1024/h/460

---

## 类的声明

kotlin用关键字`class`声明，声明一个类可以只声明头的部分，类的声明默认是`final`的。就像：

```
class Persion
//或者
class Person(name: String)
```
<!--more-->

类的构造函数可以有很多，但是只有一个可以声明在类的头部，这个构造函数被称做“主构造函数”，其他的被称做“次构造函数”。构造函数使用`constructor`关键字，主构造函数在没有可见性修饰符和注解的情况下，`constructor`可以被省略。默认的情况下，所有的构造函数的可见性都是`public`，对于使用方来说是与类的可见性保持一致。

```
class Customer public @Inject constructor(name: String) { ... }
```
主构造函数没有自己的函数体，它的参数可以在类的初始化块（`init`修饰）访问，在类的属性初始化时也可以访问。所以，在主构造函数里面想要做的事情，可以放在类的初始化块中实现。

```
class Customer(name: String) {
	val customerKey = name.toUpperCase()
    init {
        print("Customer initialized with value ${name}")
    }
}
```

如果我们想要在类的全局都可以访问主构造函数的参数，可以在参数前面加上`val`或者`var`，这样主构造函数的参数就和类的属性一样了。

```
open class Person(val name: String,val age: Int){
    fun showName(){
        print("my name is $name")
    }

    open fun showAge(){
        print("my age is $age")
    }
}
```
次构造函数必须要使用`constructor`修饰，并且必须直接或者间接的委托给主构造函数。

```
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```
## 继承

### 类的继承

kotlin中所有的类都有一个父类`Any`，类似于java中的Object，但不存在对等关系。Any中只有`equals()`、`hashCode()`和`toString()`三个方法，所以其他的Object的方法都不能直接调用。详情请参见[Java interoperability](https://kotlinlang.org/docs/reference/java-interop.html#object-methods)。后面有机会我们会再讲到。

kotlin默认类都是`final`的，为了可以被继承，我们需要在类的声明前面加上`open`，让该类可以被其他类继承。

```
open class Base(p: Int)
```
如果父类有主构造函数的话，则必须在子类声明的头部被初始化。

```
class Derived(p: Int) : Base(p)
```
次构造函数也必须直接或间接的初始化父类的构造函数：

```
class MyView : View {
    constructor(ctx: Context) : super(ctx) {
    }

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs) {
    }
}
```

### 复写方法

同类一样，子类只能复写父类中被`open`修饰的函数，复写方法必须使用`override`。

```
open class Person(name: String, age: Int){
    var name = name;
    val age = age;
    fun showName(){
        print("my name is $name")
    }

    open fun showAge(){
        print("my age is $age")
    }
}

class Women(name: String, age: Int) : Person(name, age){
    override fun showAge(){
        print("my age is 18")
    }

    override fun showName(){
        // error, compiler complain
    }
}
```
子类中复写的方法，默认也是`open`的，如果需要，可以在方法`override`之前添加`final`注解，让该子类的子类不能再复写该方法。

```
final override fun v() {}
```

### 多继承

kotlin和java8一样，本身并不能同时继承于多个类，但是可以实现多个接口，而且接口可以有自己的实现，所以当父类和接口或者接口和接口中的方法一样时，会发生冲突，我们需要明确这个时候的解决方案，不然就会`compiler complain`。

```
interface Young{
    fun showAge(){
        print("my age is between 13 and 25")
    }
}

class Student(name: String, age: Int) : Person(name, age), Young{

    override fun showAge() {
        super<Person>.showAge()
        super<Young>.showAge()
    }
}
```

## 委托

委托被认为是一个非常好的替代继承和实现的设计模式，kotlin也支持这种模式。

```
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main() {
    val b = BaseImpl(10)
    Derived(b).print() // prints 10
}
```

在类的声明过程中，在父类类型后面使用`by`关键字指明在Derived的对象中将会内部存储`b`对象，并且编译器会将`Base `的所有方法指向`b`。

