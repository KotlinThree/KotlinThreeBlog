title: 我与 Kotlin 的爱恨情仇之浅谈 Type aliases
date: 2017-05-25 21:24:29
tags: 
- Kotlin
---

   > 在上一篇文章 [《我与 Kotlin 的爱恨情仇之浅谈 block》](http://shanghai.kotliner.cn/2017/05/25/sk_love_block/)最后, 我提到了 `Kotlin` 已经引入 `Type aliases` ，今天就跟大家一起来聊聊吧。

## Type aliases 是什么？

接受一个新的东西的时候，我们首先都会从字面去猜测它大概是用来做什么的，`Type aliases` 也不例外。
 `Type` 顾名思义 `类型` , `aliases` 我想大伙也并不会太陌生，意味着 `别名`，很多时候我们会在系统的 `profile` 中加入一些 `aliases`，方便我们使用命令。二者联合起来就是： `给一个类型起一个别名`。

<!-- MORE -->

 ## 我爱 Type aliases 

 * 怎么用？

 使用关键字 `typealias`，举个例子：

```
    typealias NodeSet = Set<Network.Node>

    typealias Predicate<T> = (T) -> Boolean
```

* 我写项目的时候已经用到了哪些呢？

 `block` 闭包更加简洁，增加可复用，可读性。
 
 上一篇文中讲解有趣的 `block`，总结中提到让人头疼声明冗长，可读性差的问题。举个例子：让你去封装一个网络下载的API，方法是异步更新状态。

 ![](https://ws2.sinaimg.cn/large/006tNbRwgy1ffvimjc007j31kk036t9c.jpg)

 一个方法还是可以接受，假如由于需求的变更，需要重载很多方法出来的时候怎么办呢？难道要这样子么？

 ![](https://ws3.sinaimg.cn/large/006tNbRwgy1ffvisrohxxj31kw09bgni.jpg)

WTF,当然是review都不能通过的代码，看着新好累，当然在1.1之前抛开使用 block 而使用 interface，估计也只能选择无奈的review通过代码。但现在不用了的，使用 `typealias` 完美解决：

![](https://ws2.sinaimg.cn/large/006tNbRwgy1ffw359wvmfj31g40dodik.jpg)


更多例子还是看官方文档吧：[《reference/type-aliases》](https://kotlinlang.org/docs/reference/type-aliases.html)

 ## 我不爱 Type aliases 

只是按照之前的吐槽方式而已，才有了这一个节点，可以忽略，因为我很爱 `Type aliases`。如果非要我吐槽的话，只能说来的太迟了把。哈哈哈。

## 写在最后

`Type aliases`的使用，能够让代码写起来更舒服。但切记不要滥用，比如文档中提到的 `typealias AInner = A.Inner`, 用的好的话还好，用的不好的话，别个接手你的代码要去跳转到定义里面才能看到这个别名到底是干嘛的。但它真是个好利器，赶紧用起来吧。

