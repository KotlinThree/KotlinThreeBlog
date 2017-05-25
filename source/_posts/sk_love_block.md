title: 我与 Kotlin 的爱恨情仇之浅谈 block
date: 2017-05-23 21:34:29
tags: 
- Kotlin
---

## 前言

hi, IMSK。是的，你没看错，没迷路，这里是 `Kotlin`  ，不是 `Objective-C`，别怕接下来跟我一起认识认识这个 block 的前世今生。前方高能，请您带上耳机，戴上眼镜。

<!-- MORE -->

## block是什么？

先来看看 Kotlin 代码长什么样子：
     
![](http://upload-images.jianshu.io/upload_images/2874486-92b0b5724169c608.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中 `body` 就是一个 block， `() -> T` 是一个 函数块（ 函数签名 ）。block 可以当做参数传入， 也可以当做返回值返回。在这里我就沿用我之前写 Objective-C 的习惯，暂且称作他为 闭包。

## 我爱 block

再来对比一下 Objective-C 里的 block：

double (^multiplyTwoValues)(double, double);

![](http://upload-images.jianshu.io/upload_images/2874486-9893735489e0c33f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


如果之前没有写过 OC，那么我想第一反应只能无奈的说一句：不觉明历。回头来看看，还是 Kotlin 更加亲切一些，直接  `() -> T ` ，`{}`， 随便怎么玩，详细用法看官网吧
[《Higher-Order Functions and Lambdas》](https://kotlinlang.org/docs/reference/lambdas.html) 。

 为什么爱上 block， 举个简单例子: 我们经常要处理一个异步请求，等数据返回的时候，回调给调用方，如果是用 Java 来写，可能要用到接口（callback）来实现了的。那么在 Kotlin 里怎么办呢？

 声明：

![](http://upload-images.jianshu.io/upload_images/2874486-e2d2fbc43ee9ba8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


调用：

![](http://upload-images.jianshu.io/upload_images/2874486-ca3216bbf543510f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


看到了么？不用在像以前那样还要单独去写一个 `callback`  的 `interface ` 类了，是不是很简洁。

## 我不爱 block

 当然很大一部分原因是因为当年被 OC 中的 block 折磨的心累，写法让人难受不说，OC 先天的冗长代码实在是累，如果是个新手，还经常内存泄露，折磨的死去活来的。这里提一下，block是一个闭包，开发过程中，切记由于闭包是可以访问上文数据，处理不当就会导致内存泄露哦。

 当然还有另外一方面的原因，就是声明多参数的时候，比较难受，OC实在是不想在提了的，事实上 Kotlin 还算可以接受的，比如：

* Kotlin 中 block 多参数声明

 ![](http://upload-images.jianshu.io/upload_images/2874486-50b716e7833401b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Kotlin 中 block 多参数调用

 ![](http://upload-images.jianshu.io/upload_images/2874486-dd20a3173a6a8837.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 写在最后

Kotlin 中 block 随处可见 `{ ..do somethings.}`，所以咱必须得掌握它.

比如结合提供的列表操作的语法糖：

    strings.filter{ it.length == 5 }.sortBy{ it }.map{ it.toUpperCase() }

比如在 Android 中写一个延迟的 `Runnable`：

    postDelayed({
        //do somethings block
    },300)


 block 让代码写起来更加方便，更加灵活（比如尾闭包等），函数式编程三板斧离不开它，但 block 同时带来的弊端也是有的，比如可读性差/内存管头疼等。但个人愚见，利大于弊，虽然一直褒贬不一，饱受争议的一个神奇的东西。既然提到 block 那么当然离不开另外一个神奇的东西 `typedef` 。还好最新的 Kotlin 中已经有了 一个类似的东西 `Type aliases` (([Type aliases (since 1.1) - Kotlin Programming Language](https://kotlinlang.org/docs/reference/type-aliases.html)) . 如果大家感兴趣的话，那我下一章来谈谈这个 block 离不开的小情人吧。

 BTW  上面文中提到，block 会有内存泄露问题，无论还是 OC 是 Kotlin，当然我并没有提到如何解决，留给你第一个想象空间，那么第二个想象空间就是，既然有内存泄露，那么 block 的实现原理又是什么呢？ 跟匿名内部类有什么区别呢？

