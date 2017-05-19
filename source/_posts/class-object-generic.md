title: 类与对象——泛型
date: 2015-12-22 23:08:50
tags:
- kotlin
- generics
- 泛型
- 类与对象

---


与java一样，Kotlin也提供泛型，为类型安全提供保证，消除类型强转的烦恼。

### 泛型定义

好吧，如果只是简单声明一个泛型，和`Java`没有什么大的区别，你可以这样声明：

```
class Box<T>(t: T) {
    var value = t
}
```
然后可以这样使用

<!--more-->

```
val box: Box<Int> = Box<Int>(1)

// 或者

val box = Box(1) // 编译器会进行类型推断
```

### 泛型约束

和类的继承一样，`Kotlin`中使用`:`代替`extends`对泛型的的类型上限进行约束。

```
class SwipeRefreshableView<T : View>{}
```
不过这里你可以进行多个类型的上限约束：

```
class SwipeRefreshableView<T>
    where T : View,
          T : Refreshable {

}

// 或者

fun <T> cloneWhenGreater(list: List<T>, threshold: T): List<T>
    where T : Comparable,
          T : Cloneable {
  return list.filter { it > threshold }.map { it.clone() }
}
```
到这里，对于之前用过泛型的同学来说都没有什么难度。so，kotlin还有什么java里没有的东西吗？

### `in`和`out`

`Kotlin`中引入两个新的泛型修饰符`in`和`out`，要解释这两个关键字的用法，我们先从另外两个概念说起‘covariant（协变性）’和‘contravariance（逆变性）’（不知道的可以[参考](http://www.cnblogs.com/Figgy/p/4575719.html)）。我们都知道在java中List不是协变的，而Array是协变的：

```
Integer[] intArray = new Integer[10];
Number[] numberArray = intArray;
numberArray[0] = 1.0f;
```
在上面的代码中，`Integer[]`被认为是`Number[]`的子类型，所以可以将`intArray `赋值给`numberArray`，但是在随后的代码，我们将`1.0f`赋给`numberArray[0]`，因为在这里看来，将一个浮点型赋给一个Number对象不会有什么问题。最后悲剧发生了，当执行时，程序crash了。

但是当你使用泛型的的时候：

```
List<String> strs = new ArrayList<>();
List<Object> objs = strs; // error, compiler complain
```
`List<String>`并不是`List<Object>`的子类型，于是编译器告诉你，不能直接赋值。或许你会说我们可以使用通配符`? extends T`让它变得协变。

```
List<String> strs = new ArrayList<String>();
strs.add("0");
strs.add("1");
List<? extends Object> objs = strs;
//编译通过
```
`List<String>`是`List<? extends Object>`的子类，所以上面的代码的确能够编译运行，但是当你尝试为`objs`添加内容时：

```
//然后添加一个int型试试
objs.add(1); // error, compiler complain
// 编译器编译出错

// 现在再添加一个String
objs.add("1"); // error, compiler complain
// 编译出错
```
对于objs并不会因为`objs = strs;`的赋值，而将`objs`的泛型类型转化为`String`类型，所以在不能判断objs的泛型类型的情况下，往objs添加任何类型的对象都是不被允许的。但是我们明确知道objs的所有类型上限（upper bound），于是我们可以通过`objs.get(0)`获取Object的对象。

 小结一下，我们可以用通配符`? extends T`让泛型类变得协变，但是对于具体泛型类型的对象我们不能赋值，只能获取。于是在下面的假设中java就可以这么写：

```
interface Source<T> {
    public T getT();
    public void setT(T t);
}

public void copy(Source<String> strs){
    Source<? extends Object> objs = strs;
    objs.setT("a"); // error, compiler complain
	String str = (String) objs.getT();
}
```
在`Kotlin`中就可以这么写：

```
abstract class Source<T> {
    abstract fun getT(): T
    abstract fun setT(t: T)  
}

fun copyT(strs: Source<String>){
    val objs: Source<out Any?> = strs;
    objs.setT("a") // error, compiler complain
    objs.getT()
}
```
上面的`out Any?`可以用`*`代替。

如果我们可以确定`Source`这个类不会有`abstract fun setT(t: T)`类似的操作，我们可以这样写：

```
abstract class Source<out T> {
    abstract fun getT(): T
    // 如果下面出现会编译不过
    // abstract fun setT(t: T) // error, compiler complain
}

fun copyT(strs: Source<String>){
    val objs: Source<Any> = strs;
    objs.setT("a") // error, compiler complain
    objs.getT()
}
```

小结一下，在定义泛型类`C<T>`时，当我们在泛型类型`T`前面添加`out`，`C`为`T`的协变类。在该类的作用域内，类型`T`只能作为该类中函数的返回类型，不能作为参数传递进来，这时也称做`C`为`T`的生产者（Producer）。

以此类推，在定义泛型类`C<T>`时，当我们在泛型类型`T`前面添加`in`，`C`为`T`的逆变类。在该类的作用域内，类型`T`只能作为该类中函数的参数传递进来，不能作为返回类型，这时也称做`C`为`T`的消费者（Consumer）。

类似于`java`中的[PECS](http://www.importnew.com/8966.html)（Producer Extends，Consumer Super），我们可以总结出：‘Consumer in, Producer out’。

如果在泛型类型使用测，在对应泛型的具体类型前面使用`out`，则等同于使用`java`中的`extends`字段，`in`则等同于`super`。

```
fun copy(from: Array<out String>, to: Array<in String>) {
    assert(from.size == to.size)
    for (i in from.indices)
        to[i] = from[i]
}

// 等同于

public void copy(List<? extends String> from, List<? super String> to) { ... }

```

PS： 这里Array 与 List 不是对等关系。

## 欢迎大家关注我们的公众号

![](http://7xpox6.com1.z0.glb.clouddn.com/qrcode_for_gh_b2ad0581a6c4_430.jpg?imageView2/2/w/320) 


## 参考资料

- [java泛型](http://www.cnblogs.com/panjun-Donet/archive/2008/09/27/1300609.html)
- [不变性、协变性和逆变性（Invariance, Covariance & Contravariance](http://www.cnblogs.com/Figgy/p/4575719.html)
- [Java 泛型: 什么是PECS（Producer Extends, Consumer Super）](http://www.importnew.com/8966.html)
- [Kotlin-Generics](https://kotlinlang.org/docs/reference/generics.html)
