title: Kotlin 函数式编程三板斧
date: 2016-07-05 22:06:41
tags: 
- 函数式编程
- Kotlin
  
thumbnail: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-160627353.jpg?imageView2/1/w/200
banner: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-160627353.jpg?imageView2/1/w/1024/h/460 

---


tags: kotlin, 函数式编程, map, filter, reduce

每个函数式语言都提供及几大类基本函数，这些函数在功能上一般都极为相似，但是在名称和调用方法上可能有一些细微的差别。今天就讲讲 `Kotlin` 中提供的函数式编程三板斧`filter`、`map`、`reduce`。

<!-- more -->

## Filter

筛选函数将用户给定的布尔逻辑作用于集合，返回由原集合中符合条件的元素组合的一个子集。假设一个逻辑，将数组中是3的倍数的数筛选出来，和 `Java` 做一个简单的对比。

```java
//Java 代码
int[] all = {1, 2, 3, 4, 5, 6, 7, 8, 9};
List<Integer> filters = new ArrayList<>();
for (int a : all) {
    if (a % 3 == 0) {
        filters.add(a);
    }
}
```

```kotlin
// Kotlin 代码
val all = arrayOf(1, 2, 3, 4, 5, 6, 7, 8, 9)
val filters = all.filter { it % 3 == 0 }
```

Kotlin 还提供一系列类似的过滤函数：

- `filterIndexed`, 同 `filter`，不过在逻辑判断的方法块中可以拿到当前item的index
- `filterNot`，与`filter`相反，只返回不符合条件的元素组合

针对 `Map` 类型数据集合，提供了 `filterKeys` 和 `filterValues` 方法，方便只做 key 或者 value 的判断。

## Map

映射函数也是一个高阶函数，将一个集合经过一个传入的变换函数映射成另外一种集合。

假设我们现在需要将一系列的名字的长度保存到另一个数组。

```java
// Java 代码
String[] names = {"James", "Tom", "Jack", "Kobe"};
int[] namesLength = new int[names.length];
for (int i = 0; i < names.length ; i ++) {
    namesLength[i] = names[i].length();
}
```

```kotlin
// Kotlin 代码
val names = arrayOf("James", "Tom", "Jack", "Kobe");
val namesLength = names.map { it.length }
```

映射函数是将一个集合映射成另外一种集合，所以当你需要见一个 HaspMap 映射成一个 List 的时候，会非常方便。

```kotlin
val params = mapOf("limit" to "10", "offset" to "20", "order" to "asc")
val s = params.map { "${it.key}=${it.value}" }.joinToString("&")
// s = limit=10&offset=20&order=asc
```

同 `filter` 相似，Kotlin 也提供的 `mapIndexed` 的类似方法方便使用，针对 `Map` 类型的集合也有 `mapKeys` 和 `mapValues` 的封装。

## Reduce

归纳函数将一个数据集合的所有元素通过传入的操作函数实现数据集合的积累叠加效果。

假设我们需要将一首藏头诗的每句诗的第一句拿出来拼成一句话。

```java
// Java 代码
String[] texts = {"芦花丛中一扁舟", "俊杰俄从此地游", "义士若能知此理", "反躬难逃可无忧"};
StringBuffer sb = new StringBuffer();
for (int i = 0; i < texts.length ; i ++) {
    sb.append(texts[i].substring(0, 1));
}
String result = sb.toString();
```

```kotlin
// Kotlin 代码
val texts = arrayOf("芦花丛中一扁舟", "俊杰俄从此地游", "义士若能知此理", "反躬难逃可无忧")
val result = texts.map { it.substring(0,1) }.reduce { r, s -> "$r$s"}
```

最后得到的结果是「卢俊义反」。

## 函数式编程

函数式编程的精髓在于函数本身。在函数式编程中函数是第一等公民，与其他数据类型一样，处于平等地位，可以赋值给其他变量，也可以作为参数，传入另一个函数，或者作为别的函数的返回值。

函数式编程好的实践在于对运算过程的高度抽象和没有"副作用"（既保持函数的独立性），函数式编程三板斧是函数式编程的典型范式，在编程中被大量使用，即使人们不关注函数式编程，在使用函数式编程语言的时候，也会不自觉的使用这些函数。

函数式编程是一种思维方式，函数式编程鼓励放弃对状态的维持（是命令式编程的基础），将所有的操作都交给运行时去执行。当然为了保证程序运行的效率，这需要提供一些辅助性的手段（缓存、缓求值等）。

[参考资料]

- [函数式编程思维](https://book.douban.com/subject/26587213/)
- [函数式编程](http://baike.baidu.com/view/1711147.htm)
- [kotlin doc](https://kotlinlang.org/docs/reference/)
- [kotlin api doc](https://kotlinlang.org/api/latest/jvm/stdlib/)
