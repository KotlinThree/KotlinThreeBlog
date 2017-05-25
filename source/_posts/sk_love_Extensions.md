# 我与 Kotlin 的爱恨情仇之浅谈 Extensions
>Kotlin, similar to C# and Gosu, provides the ability to extend a class with new functionality without having to inherit from the class or use any type of design pattern such as Decorator. This is done via special declarations called extensions. Kotlin supports extension functions and extension properties.

上一个章节[《我与 Kotlin 的爱恨情仇之浅谈 Type aliases》](http://shanghai.kotliner.cn/2017/05/25/sk_love_Type%20aliases/)中，带领大家一起领略了一下 `typealias`的基本用法。可以让你写出更加优美的代码。今天再带大家一起来领略一下 `Kotlin` 中另一个神奇好玩的东西: `Extensions`.

## Extensions 是什么？

千万不要被 `JAVA` 中的 `extend` 给带歪了的，一个是扩展，一个是继承，完全不是一码事。而且这也是我讨厌的一点，至于原因我接下来就会讲到，当然纯属我个人认知喜好问题。

文章口头就引用了[《reference/extensions》](https://kotlinlang.org/docs/reference/extensions.html)中的一段话，已经解释了 `Kotlin` 具备可以 `extension` (扩展) `functions` 以及 `properties` 的能力。

## 我爱 Extensions

* Extension Functions 我们可以给任何一个 `类` 包括系统的类追加扩展方法

    给 `MutableList` 扩展(追加) 一个 `swap` 方法，用于交互两个 `index` 的 `value` :
    ```
    fun MutableList<Int>.swap(index1: Int, index2: Int) {
        val tmp = this[index1] // 'this' corresponds to the list
        this[index1] = this[index2]
        this[index2] = tmp
    }   

    ```

    具体用的时候:

    ```

    val l = mutableListOf(1, 2, 3)
    l.swap(0, 2) // 'this' inside 'swap()' will hold the value of 'l'

    ```

 * Extension Properties 我们可以给任何一个 `类` 包括系统的类追加属性，但切记属性必须按约定来

     我们可以给给 `List` 追加一个 `lastIndex` 属性

    ``` 
    val <T> List<T>.lastIndex: Int
    get() = size - 1
    ```    

    但是我说的约束是不能这样子写 

    ```
     val Foo.bar = 1 // error: initializers are not allowed for extension properties
    ```

    更多注意事项请看 [《官方文档》](https://kotlinlang.org/docs/reference/extensions.html)


   ### 我们项目中拿他来干什么呢？
  `Google` 一搜索一大把，比如出了名的 [anko](https://github.com/Kotlin/anko) ，里面提供大量的 `Extension`。 但我并没有用过 `anko`，虽然使用 `Kotlin` 写 `Android` 项目已经超过两年了的，原因是个人觉得太重了的，我们自己也封装了一个轻量级的 [AndroidExtension](https://github.com/KotlinThree/AndroidExtension)

  比如万恶的 `findViewById`,可以写成这样：

           val textview = findview(R.id.txt)

 比如臭长的 `Toast`, 现在也可以简单到这样啦：

          toast("hello,I'M SK")

 还有更多有意思的，可以大大提高开发效率的扩展，具体可以查看我们的 [KotlinThree GitHub](https://github.com/KotlinThree)

 ## 我不爱 Extensions
no, no, no 不能不爱，用起来实在是太爽了的，极大的提高了我的日常开发效率，但非要让我挑毛病的话，我只能说因为当初的 `Swift` 坑了我吧，我第一反应是跟 `Swift extension` 一样，终于可以摆脱之前要在一个类的开头，写一堆的接口，类似这样:

![image.png](https://ws4.sinaimg.cn/large/006tNbRwgy1ffwstpktgkj31060mk79f.jpg)
    但在 `Swift` 中可以用 `extension` 完美解决这个问题：
    ![](https://ws3.sinaimg.cn/large/006tNbRwgy1ffwsub7k3yj317k0j641m.jpg)
好吧，所以在没有看仔细看 `Kotlin` 文档的时候，我以为 `extension` 用法和 `Swift` 一样呢，但完全不是一个概念。`Kotlin` 中压根就没有这个 `extension` 关键字。

## 写在最后

 这个语法糖，你可以随便去扩展你自己想要的功能，从而极大的提高我们开发效率，用起来确实让人爱不释手。那么 `Kotlin`  内部是怎么实现的？ 可以看看编译出来的文件，其实是帮忙做了一层转换，把我们的扩展的方法做成一个 `static method`, 然后调用的地方其实是调用生成的的这个 `static method`，之前[《我与 Kotlin 的爱恨情仇之浅谈 block》](http://www.jianshu.com/p/53c657bed4ab)谈到的另一把我爱不释手的利器 `block` 在评论中我也说了的，都是人家最后帮我们去生成了代码，然后一样的做法，虽然效率并没有任何提升，有兴趣的话，您不妨自己反编译试试。但却不得不说，大大提高了我们本身编码效率，毕竟重复的代码，谁写着都觉得心累。