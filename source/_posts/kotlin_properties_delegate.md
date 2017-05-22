title: 玩转 Kotlin 委托属性
date: 2017-05-22 21:04:43
tags: 
- Kotlin
  
thumbnail: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-170046969.jpg?imageView2/1/w/200
banner: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-170046969.jpg?imageView2/1/w/1024/h/460 

---


tags: Kotlin

## Kotlin 属性

要讲 Kotlin 的委托属性，要先从 Kotlin 的属性说起，当然关于属性的定义就不多介绍了。这里介绍一下 Kotlin 区别于 Java 独有的 back field 的概念。用过 Kotlin 的人都知道，Kotlin 的属性是天生带 Setter/Getter 方法的，不过如果要重写他们的话，写法有所不同。

<!-- more -->

```
var a: String = "1"
	get() = field
	set(value) {
        field = value
	}
```

我们可以看到，当需要重写 Setter/Getter 方法的时候，就需要用到 field 这个新概念，它其实是代表这个域本身。有些人刚开始看到这个东西的时候，可能会觉得很神秘，其实它里面的实现逻辑很简单，就是对应到 Java 中 Setter/Getter 方法，然后 field 在 Java 的方法中就是该属性本身，上面的代码编译后的代码：

```
@NotNull
private String a = "1";

@NotNull
public final String getA() {
  return this.a;
}

public final void setA(@NotNull String value) {
  Intrinsics.checkParameterIsNotNull(value, "value");
  this.a = value;
}
```

基于这样的逻辑，对于 `Kotlin` 属性的 `lateinit` 修饰符的实现原理，就可以很简单的推理出来，在属性的 `Getter` 方法中先判断该属性是否被赋值，否则的话抛出异常，下面就是一个用 `lateinit` 修饰的属性生成的 `Getter` 方法。

```
@NotNull
public final String getPropLateInit() {
  String var10000 = this.propLateInit;
  if(this.propLateInit == null) {
     Intrinsics.throwUninitializedPropertyAccessException("propLateInit");
  }

  return var10000;
}
```

讲到这里，反应快的人应该能猜到到，下面要讲的属性委托是基于什么原理实现的了。

## Kotlin 委托属性

### 委托属性的声明

定义一个委托属性的语法是 `val/var <property name>: <Type> by <expression>`，其中 `by` 后面的就是属性的委托。属性委托不用继承什么特别的接口，只要拥有用 `operator` 修饰的 `getValue()` 和 `setValue()` (适用 var)的函数就可以了。

需要注意的是在官方的文档里，要求 `getValue()` 和 `setValue()` 两个函数提供固定的参数，就像下面的例子一样。但是事实其实并非如此，这里我们先按照官方的说法继续，后面再解释这里的差异。

```
class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }
 
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name} in $thisRef.'")
    }
}
```

对于参数的描述这里做一个简单描述：

- `thisRef`，属性的拥有者；
- `property`，对属性的描述，是 KProperty<*> 类型或是它的父类；
- `value`，属性的值。

### 委托属性的背后实现

Kotlin 官方在官方标准库里提供委托属性的三个常用场景，作为委托属性的范例。这里重点分析一下 `lazy` 的背后的实现原理，然后顺带讲一下 `Observable` 和 `storing` 的用法。

#### lazy

通过 `lazy` 我们可以定义一个懒加载的属性，该属性的初始化不会再类创建的时候发生，而是在第一次用到它的时候赋值。

```
val propLazy: Int by lazy{1}
```

我们查看一下编译后的 `bytecode`。

```
	LINENUMBER 4 L1
	ALOAD 0
	GETSTATIC PropertiesDemo$propLazy$2.INSTANCE : LPropertiesDemo$propLazy$2;
	CHECKCAST kotlin/jvm/functions/Function0
	INVOKESTATIC kotlin/LazyKt.lazy (Lkotlin/jvm/functions/Function0;)Lkotlin/Lazy;
	PUTFIELD PropertiesDemo.propLazy$delegate : Lkotlin/Lazy;
L2
```

字节码的可读性太差，我们反编译一下，找到相关的代码。

```
public PropertiesDemo() {
  this.propLazy$delegate = LazyKt.lazy((Function0)null.INSTANCE);
}
   
@NotNull
private final Lazy propLazy$delegate;

static final KProperty[] $$delegatedProperties = new KProperty[]{(KProperty)Reflection.property1(new PropertyReference1Impl(Reflection.getOrCreateKotlinClass(PropertiesDemo.class), "propLazy", "getPropLazy()I")))};
   
public final int getPropLazy() {
  Lazy var1 = this.propLazy$delegate;
  KProperty var3 = $$delegatedProperties[0];
  return ((Number)var1.getValue()).intValue();
}
```

可以看到 Kotlin 为我们生成了一个 `Lazy` 类型的 `propLazy$delegate` 属性，同时生成一个 `getPropLazy()` 方法，但是我们并没有找到 `propLazy` 属性的定义（这一点我们先不管，后面再说）。

在 `getPropLazy()` 的实现里可以看到返回的是 `propLazy$delegate.getValue()` 的值，再看下 `propLazy$delegate` 的赋值是在类的构造函数里面 `this.propLazy$delegate = LazyKt.lazy((Function0)null.INSTANCE);`。LazyKt 是系统的 `Lazy.kt` 文件生成的类文件，找到 `Lazy.kt` 的 `lazy()` 方法，返回的是 `SynchronizedLazyImpl` 的实例。

```
@kotlin.jvm.JvmVersion
public fun <T> lazy(initializer: () -> T): Lazy<T> = SynchronizedLazyImpl(initializer)
```

在 `SynchronizedLazyImpl` 实现代码里，通过 `_value` 用来真正保存属性的值。`_value` 的默认值是 `UNINITIALIZED_VALUE` (一个自定义的对象)。当 `_value` 不是默认值的时候，就会直接把 `_value` 的值作为 `getValue()` 的返回；当 `_value` 还是默认值的时候，就会调用 `initializer` 初始化表达式完成初始化，赋值给 `_value` 并作为 `getValue()` 的返回。

```
private object UNINITIALIZED_VALUE
private class SynchronizedLazyImpl<out T>(initializer: () -> T, lock: Any? = null) : Lazy<T>, Serializable {
    private var initializer: (() -> T)? = initializer
    @Volatile private var _value: Any? = UNINITIALIZED_VALUE
    // final field is required to enable safe publication of constructed instance
    private val lock = lock ?: this

    override val value: T
        get() {
            val _v1 = _value
            if (_v1 !== UNINITIALIZED_VALUE) {
                @Suppress("UNCHECKED_CAST")
                return _v1 as T
            }

            return synchronized(lock) {
                val _v2 = _value
                if (_v2 !== UNINITIALIZED_VALUE) {
                    @Suppress("UNCHECKED_CAST") (_v2 as T)
                }
                else {
                    val typedValue = initializer!!()
                    _value = typedValue
                    initializer = null
                    typedValue
                }
            }
        }

    override fun isInitialized(): Boolean = _value !== UNINITIALIZED_VALUE

    override fun toString(): String = if (isInitialized()) value.toString() else "Lazy value not initialized yet."

    private fun writeReplace(): Any = InitializedLazyImpl(value)
}
```


我们发现 `SynchronizedLazyImpl` 的 `getValue()` 方法并没有带参数，在反编译的 `getPropLazy()` 代码中 `KProperty var3 = $$delegatedProperties[0];` 这个变量其实根本没有用到，其实在正常的委托的反编译的代码是类似这样的。

```
return (String)this.propObservable$delegate.getValue(this, $$delegatedProperties[1]);
```

所以说其实我们在定义委托的时候，`getValue()` 和 `setValue()` 方法是可以不带参数的，只是官方在编译阶段做了限制，导致我们只能拥有带参数的方法。为了验证如果这个想法，我参考 `lazy` 实现了一个类似的功能，发现根本不能通过编译。

##### 关于 `propLazy` 属性本身

前面我们有提到在生成的字节码中，并不能找到 `propLazy` 这个属性的定义，我们先看看官网怎么说的。

```
class C {
    var prop: Type by MyDelegate()
}

// this code is generated by the compiler instead:
class C {
    private val prop$delegate = MyDelegate()
    var prop: Type
        get() = prop$delegate.getValue(this, this::prop)
        set(value: Type) = prop$delegate.setValue(this, this::prop, value)
}
```

根据官方的文档描述，`Kotlin` 会自动生成 `prop$delegate` 属性，并复写 `prop` 的 `Setter/Getter` 方法。按照这个说话的话，我们上面在编译后的字节码里面应该是可以找到 `propLazy` 属性的。

为了验证这个问题，我首先想到是不是因为这个属性是私有变量，在类里面没有使用，所以 `Kotlin` 编译器为了优化生成字节码的数量而故意去掉了呢。于是我故意在另外一个方法里尝试输出该属性，但是最后发现在编译后该处的使用被替换成 `getPropLazy()` 方法的调用，所以看来 `propLazy` 是真的没有了。

为了进一步验证这个想法，我们还在运行时用反射的方法去获取该属性，发现的确找不到该属性，最后我们得出结论是委托属性在编译后会生成对应的 `prop$delegate` （被委托的属性」），然后生成生成委托属性的 `Setter/Getter` 方法，但是该属性本身并不在类的域定义里面，这个时候尝试用反射的方法直接拿到这个属性是做不到的（当然你可以通过 `prop$delegate` 反射到你想要的内容）。

### Observable

官方推荐另外一个委托属性的应用就是 `Observable`，让属性在发生变动的时候可以被关注的地方观察到。

```
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

上面代码的输出：

```
<no name> -> first
first -> second
```

想了解 `Observable` 的实现方式，大家可以参考前面分析 `lazy` 的方法，去探究一下。关于 `Observable` 的进一步实现场景，我们一直有一个想法，就是基于这个特性封装出一套 MVVM 的框架，等到这个框架实现以后，再和大家分享。

### Storing

`Storing` 的使用场景是被模型的属性全部委托到 `Map` 的结构去真实的存储数据，用于解析 `Json` 或者做一些动态的事情。

```
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
``` 

不过根据我的了解，一些 Json 的解析库是直接用反射的方式实现的反序列化，根据我们前面的分析，这里根本解析不出来，所以这个场景看来是使用不了了。

## 关于 `BufferKnife` 和 `KotterKnife`

### `BufferKnife` 

在 `Kotlin` 刚推出来的时候，由于不支持 `apt` ，所以会导致 `BufferKnife` 这类用注解实现的框架会使用不了，但是  `Kotlin` 很快就意识到这个问题并推出 `kapt`。在 `kapt` 推出来以后其实 `BufferKnife` 就可以正常使用了，我们也在我们的代码里使用了 `BufferKnife`。但当时 `BufferKnife` 在增量编译的时候有时候的确会出一些问题，导致我们那个时候最后选择了放弃，我们自己简单封装下 `findViewById` 的操作，有兴趣的可以看下 [AndroidExtension](https://github.com/KotlinThree/AndroidExtension) 。可能有些人关于 `Kotlin` 和 `BufferKnife` 的冲突信息是来自我们当时不准确的描述，导致认为他们不能一起使用。而且经过这么久的迭代，我相信官方应该早就解决这个问题了。

### `KotterKnife`

`KotterKnife` 这个库的存在可能也是很多人认为 `Kotlin` 不能使用 `BufferKnife` 的一个因素。在我看来 `KotterKnife` 创建的时机是 `Kotlin` 还不支持 `apt` 的时候，在  `Kotlin` 推出 `kapt` 以后这个库就已经不怎么更新了，而且这个库从来没有发布过一个正式版本，所以可以看出这只是大神在用 `Kotlin` 做的一些新的尝试而已（这一点我是通过查看代码发现 `KotterKnife` 主要使用「委托属性」这个特性猜想出来的，仅供参考）。

---

![](http://7xpox6.com1.z0.glb.clouddn.com/kotlin-three-wechat.jpg)

## 参考资料

- [官方文档](https://kotlinlang.org/docs/reference/)
- [kotterknife](https://github.com/JakeWharton/kotterknife)
- [butterknife](https://github.com/JakeWharton/butterknife)
