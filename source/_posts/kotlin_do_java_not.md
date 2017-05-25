title: 那些年Kotlin轻易能实现，java很难实现的语法糖
date: 2017-05-27 15:00:49
tags: 
- Kotlin
- Android
- java vs kotlin

thumbnail: http://7xqerh.com1.z0.glb.clouddn.com/fc_stock-photo-199570919.jpg?imageView2/1/w/200

banner: http://7xqerh.com1.z0.glb.clouddn.com/fc_stock-photo-199570919.jpg?imageView2/1/w/1024/h/460 

---

做android开发那么多年， 一直都是兢兢业业用java去码代码，直到两年前的一天发现了kotlin这门神奇的语言。犀利的语法特性，优雅的表达方式，从此掉入此坑。

这里有个[对比链接](http://www.jcodecraeer.com/demo/from-java-to-kotlin/classes.html)可以看到表达同一个含义，kotlin相对java在表达上的优势。 

最近google io大会把kotlin定为Android开发第一级语言，着实掀起了一股讨论kotlin的浪潮。那么，未来的androider是不是会用kotlin替代java呢？这里我们来讨论下一个话题：那些Kotlin轻易能实现，java很难实现的语法糖，由此来看一看kotlin的独特魅力。

<!--more-->

## 函数编程

在kotlin中，函数和类一样是一等公民，函数也可以作为参数传递（高阶函数特性），且可执行(闭包特性)。举个栗子

```
// 函数作为对象
val magic = fun(name: String) {
    println("hello $name")
}

// 函数闭包执行
magic("tom")


// 函数作为参数
fun test(funParam: (String) -> Unit) {
    funParam("tom2")
}

test(magic) 
```

在集合类的数据处理上，kotlin中的`filter`、`map`等能方便做到数据转换。举个栗子，给定一个数字数组，过滤出数字为3的倍数的数字集合，数字前加#，以逗号分隔输出。如数组为`0~10`， 打印输出`#0, #3, #6, #9`

java代码如下

```
private static String solveList(List<Integer> list) {
    // filter
    List<Integer> filterResult = new ArrayList<>();
    for (Integer i : list) {
        if (i % 3 == 0) {
            filterResult.add(i);
        }
    }

    // map
    List<String> mapResult = new ArrayList<>();
    for (Integer i : filterResult) {
        mapResult.add("#" + i);
    }

    // 逗号分隔
    StringBuilder sb = new StringBuilder();
    for (String s : mapResult) {
        sb.append(s).append(", ");
    }

    if (sb.length() > 0) {
        return sb.substring(0, sb.length() - 2);
    } else {
        return "";
    }
}
```

kotlin代码如下，异常的简洁。

```
fun solveList(list: List<Int>) : String {
    return list.filter { it % 3 == 0 }.map { "#$it" }.reduce { s1, s2 -> "$s1, $s2" }
}
```

kotlin用的是函数式思维方式，函数式三板斧`filter`, `map`, `reduce` 接受函数作为参数，屏蔽了内部实现。 比如说这里3的倍数改成5的倍数，kotlin只要改`filter`的参数（函数参数）即可，java则要改方法的内部实现。

## Extension

kotlin可对系统类进行方法扩展，如可以为集合增加`swap`函数功能

```
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
```

定义之后`MutableList`类型就可以直接调用`swap`方法

```
val list = mutableListOf("aa", "bb")
list.swap(0, 1)
```

这种形式java是无法做到的，java要实现同样的功能必须额外定义一个新类。（事实上kotlin extension原理也是编译器生成一个新类~~）

## 数据类
数据类大量重复的getter和setter相信会是很多人在开发过程中吐槽的一个点。举一个很经典的例子，我们需要一个Person的数据类。

```
public class Person {
    private String name;
    private int age;
    private int sex;
    private float height;

    public Person(String name, int age, int sex, float height) {
        this.name = name;
        this.sex = sex;
        this.age = age;
        this.height = height;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public int getSex() {
    	 return sex;
    }

    public void setSex(int sex) {
    	this.sex = sex;
    }

    public float getHeight() {
        return height;
    }

    public void setHeight(float height) {
        this.height = height;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", sex=" + (sex == 0 ? "男" : "女") +
                ", height=" + height +
                "}";
    }
}
```

在Kotlin里，我们只需要一行代码就能完成以上的功能： 
 
```
data class Person(var name: String, var age: Int, var sex: Int, var height: Float)

```

`data class`有效的提供了一种构建器模式，可以指定参数初始化，同时默认提供`clone`方法

```
//创建对象
val person: Person = Person(name="tom", age = 10, height = 1.7f)
```

## Type alias

java没alias的说法, kotlin type alias暂时很少用到，就不细讲了，有兴趣的参考官网链接 [type-aliases](https://kotlinlang.org/docs/reference/type-aliases.html)

## 字符串模板

kotlin通过`${expression}`输出字符串，`expression`表示任意表达式 如

```
println("My name's {user.name}")
println("1 + 2 = ${1 + 2}")
```

java用`String.format`形式实现

kotlin输出`list`, `map`等类型就友好的多。

```
val list = (0..10).toMutableList()
println("list=$list")
// 输出list=[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

val map = mapOf(1 to "One", 2 to "Two", 3 to "Three")
println("map=$map")

// 输出map={1=One, 2=Two, 3=Three}
```


## option参数

kotlin有option参数的说法，如定义个图片加载接口，java需要通过重载方式定义

```
interface IHJImageLoader {
    public void displayImage(String uri, ImageView imageView);

    public void displayImage(String uri, ImageView imageView, HJImageLoaderOptiono option);

    public void displayImage(String uri, ImageView imageView, HJImageLoaderListener loaderListener);

    public void displayImage(String uri, ImageView imageView, HJImageLoaderOptiono option, HJImageLoaderListener loaderListener);
}
```

而kotlin只要定义个一个方法就行

```
interface IHJImageLoader {
    fun displayImage(uri: String?, imageView: ImageView?, option: HJImageLoaderOption? = HJImageLoaderOption.defaultOption(), loaderListener: HJImageLoaderListener? = null)
}
```

调用方式都是类似

```
val imageLoaderImpl: IHJImageLoader = ...

imageLoaderImpl.displayImage(uri, imageView)
imageLoaderImpl.displayImage(uri, imageView, option)
imageLoaderImpl.displayImage(uri, imageView, option, loaderListener)
```

## 操作符重载

kotlin操作符重载在DSL中得以发扬光大, 如下面一段语句表达

```
html {
	body {
		div {
			a("http://kotlinlang.org") {
				target = ATarget.blank
				+"Main site"
			}
		}
	}
}
```

java要表达同样的含义，至少得`html`, `body`, `div`, `a`对象都`new`一遍，且要设置成员变量。冗余度和可读性上都远不如上面的表达来的舒爽。


## 协程编程
todo