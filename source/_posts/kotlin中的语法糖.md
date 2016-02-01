title: kotlin中的语法糖
date: 2016-01-14 13:49:44
tags: kotlin

---

### 基本
```
fun sum(a: Int, b: Int) = a + b
	
fun max(a: Int, b: Int) = if (a > b) a else b
	
// range in
for (x in 1..100) 
	print(x)
	
for ((k, v) in map) { 
	println("$k -> $v")
}

val list = listOf("a", "b", "c")

val a = array(1, 2, 3)

val map = mapOf("a" to 1, "b" to 2, "c" to 3)

fun transform(color: String): Int = when (color) {
	"Red" -> 0
	"Green" -> 1
	"Blue" -> 2
	else -> throw IllegalArgumentException("Invalid color param value")
}

// Null安全, user为null也不会报NullPointException
val name = user?.name ?: ""

// user.name不为null返回user.name，否者返回"unknow"
val name = user.name ?? "unknow"
	
// 字符串模板
print("my name is $name or ${user.name}")
```

### lanmda表达式

```
names
.filter { it.startsWith("A") } 
.sortedBy { it }
.map { it.toUpperCase() } 
.forEach { print(it) }
```

<!--more-->

### 单例

```
object Singlton {
	
}
```

### 静态函数

```
class MyClass {
	companion object {
		fun sayHi(){ println("") }
	} 
}
```

```
MyClass.sayHi()
```

### 方法扩展(Extension Functions)

```
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
	val tmp = this[index1] // 'this' corresponds to the list 
	this[index1] = this[index2]
	this[index2] = tmp
}

val l = mutableListOf(1, 2, 3)
l.swap(0, 2) // 'this' inside 'swap()' will hold the value of 'l'
```

### 属性扩展

```
val <T> List<T>.lastIndex: Int 
	get() = size - 1
```

### 方法重载

```
View.setOnClickListener{ println('hello') }

ps： 这种写法仅限于只有一个重载方法的情况，多方法只能用下面形式

window.addMouseListener(object : MouseAdapter() { 
	override fun mouseClicked(e: MouseEvent) {
	// ...
	}
	override fun mouseEntered(e: MouseEvent) {
	 	// ...
	}	
})
```

### 观察属性变化自动通知Observable

```
class User {
	var name: String by Delegates.observable("<no name>") {
		prop, old, new -> println("$old -> $new")
    }
}
```

运行结果

```
fun main(args: Array<String>) {
	val user = User()
	user.name = "first"
	user.name = "second"
}
```

```
<no name> -> first
first -> second
```


### 数据类

java代码

```
public class Artist {
    private long id;
    private String name;
    private String url;
    private String mbid;
 
    public long getId() {
        return id;
    }
 
    public void setId(long id) {
        this.id = id;
    }
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public String getUrl() {
        return url;
    }
 
    public void setUrl(String url) {
        this.url = url;
    }
 
    public String getMbid() {
        return mbid;
    }
 
    public void setMbid(String mbid) {
        this.mbid = mbid;
    }

}
```

等同于下面kotlin代码

```
data class Artist(var id: Long, var name: String, var url: String, var mbid: String)
```


