title: 类与对象 -- object
date: 2015-12-19 19:02:38
tags: 
- kotlin
- object 
- companion
- 单例 
- 对象

---

## 对象（object）表达式和对象声明

Kotlin中提供关键object，与java中的“Object”不同（在java中“Object”是所有类的基类，等同于Kotlin的“Any”，参考[java-interop](https://kotlinlang.org/docs/reference/java-interop.html#mapped-types)），用于直接申明一个对象，有两种使用写法：对象表达式和对象声明

### 对象表达式

通过对象表达式可以越过类的定义直接得到一个对象：

```
val point = object {
	var x: Int = 0
	var y: Int = 0
}
```
这个对象可以继承于某个基类，或者实现其他接口:

```
open class Device(var name: String) {
}

interface Vedio {
    fun play(){}
}

var television = object : Device("Sony"), Vedio{}

```
<!--more-->

这可以方便的实现一个匿名内部类的对象用于方法的参数中：

```
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
```
在对象表达中可以方便的访问到作用域中的其他变量，及时这个变量并不是`final`的。

```
fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
}
```

### 对象声明

对象声明可类似定义一个类一样定义一个对象：

```
object RCtrl : Device("Remote Control"), Infrared {
    override fun send(command: Int) {
        // send command
    }
}

object RCtrl : Device("Remote Control"), Infrared {
}
```

在使用的时候可以直接使用定义的对象：

```
fun main(args: Array<String>) {
    RCtrl.send(1)
}
```
当然你也可以定义一个变量来获取获取这个对象，当时当你定义两个不同的变量来获取这个对象时，你会发现你并不能得到两个不同的变量。也就是说通过这种方式，我们获得一个***单例***。

```
	var rCtrl1 = RCtrl
	var rCtrl2 = RCtrl
	rCtrl1.name = "TV Control"
	print("rCtrl2 name = ${rCtrl2.name}")
```

在Kotlin中我们可以方便的通过对象声明来获得一个单例。

不过需要注意的是，对象声明不能直接定义在一个函数中。

与对象表达式不同，当对象声明在另一个类的内部时，这个对象并不能通过外部类的实例访问到该对象，而只能通过类名来访问，同样该对象也不能直接访问到外部类的方法和变量。

```
class Desk{
	var legCount = 4
	object DeskTop{
       var area = 0
       fun showLegs(){
       	print{"desk legs $legCount"} // error, compile complain
       }
   }
}

fun main(args: Array<String>) {
	var desk = Desk()
   	desk.DeskTop.area // error, compile complain
   	Desk.DeskTop.area // right
}
```

### companion对象

当对象声明在另一个对象中时，我们可以通过关键字`companion`将对象与外部类关联在一起，这样我们就可以直接通过外部类访问到对象的内部元素。

```
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}

val instance = MyClass.create()
```

我们甚至可以省略掉该对象的对象名，然后使用`Companion`替代需要声明的对象名：

```
class MyClass {
    companion object {
    }
}

val x = MyClass.Companion
```

看到上面的例子我们我们就会思考如果我们定义两个内部关联对象怎么办，答案当然是不行，不管是否声明对象名，一个类里面只能声明一个内部关联对象。

```
class Desk{
    companion object DeskTop {
        var area = 0;
    }

    companion object Leg{  // error, compile complain
        var lenght = 80
    }
}
```


## 参考资料
[kotlin](https://kotlinlang.org)



