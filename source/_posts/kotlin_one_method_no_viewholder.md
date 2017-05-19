title: Kotlin 一个扩展函数，从此丢掉 ViewHolder
date: 2016-08-02 23:57:49
tags: 
- Kotlin
- Android
  
thumbnail: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-160624845.jpg?imageView2/1/w/200
banner: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-160624845.jpg?imageView2/1/w/1024/h/460 

---


tags: Android, Kotlin


## ViewHolder

作为一名 Android 开发者，对 ViewHolder 应该再熟悉不过了。ViewHolder 一开始并不是 Android 原生提供的（现在已经是 RecycleView 的默认实现了），而是 Google 为了提高 ListView 的使用性能，为开发者提供的一种最佳实践，具体可以参考 [ViewHolder](https://developer.android.com/training/improving-layouts/smooth-scrolling.html#ViewHolder)。

<!-- more -->

Google 提供的 ViewHolder 的标准实现如下，熟悉者可以直接跳到下个部分「ViewHolder变种」继续阅读。

```java
static class ViewHolder {
  TextView text;
  TextView timestamp;
  ImageView icon;
  ProgressBar progress;
}
```

在 Item 第一次创建视图的时候，填充 ViewHolder 并且将其保存在视图中。

```java
ViewHolder holder = new ViewHolder();
holder.icon = (ImageView) convertView.findViewById(R.id.listitem_image);
holder.text = (TextView) convertView.findViewById(R.id.listitem_text);
holder.timestamp = (TextView) convertView.findViewById(R.id.listitem_timestamp);
holder.progress = (ProgressBar) convertView.findViewById(R.id.progress_spinner);
convertView.setTag(holder);
```

在填充 Item 数据的时候，直接使用 Viewholder 对象的属性，这样可以减少在滚动 ListView 频繁调用 `findViewById()` 而导致的性能问题。

## ViewHolder变种

Google 提供的 ViewHolder 的确能够提升 ListView 的使用效率，但是 ViewHolder 的实现相对繁琐，需要为每一种 Item 定义一个 ViewHolder，对代码书写和维护都是额外的开销。于是有人针对 ViewHolder 的实现做了一些优化，让 ViewHolder 写起来更方便。网上有很多种写法，我最认可的是下面的这种实现，简单优雅。

```
public class ViewHolder {    
    @SuppressWarnings("unchecked")  
    public static <T extends View> T get(View view, int id) {  
        SparseArray<View> viewHolder = (SparseArray<View>) view.getTag();  
        if (viewHolder == null) {  
            viewHolder = new SparseArray<View>();  
            view.setTag(viewHolder);  
        }  
        View childView = viewHolder.get(id);  
        if (childView == null) {  
            childView = view.findViewById(id);  
            viewHolder.put(id, childView);  
        }  
        return (T) childView;  
    }  
}  
```

这里使用 `SparseArray` 映射每个视图 `id` 和对应的视图，并将其保存在视图中，这样既保证在滚动过程中频繁获取视图的效率，使用起来也极其方便。

```java
ImageView bananaView = ViewHolder.get(convertView, R.id.banana);  
TextView phoneView = ViewHolder.get(convertView, R.id.phone);  
BananaPhone bananaPhone = getItem(position);  
phoneView.setText(bananaPhone.getPhone());
```

## Kotlin 扩展函数

这里Kotlin 实现 ViewHolder 的扩展函数和上面的变种使用的同一种思路，但得益于 Kotlin 语言提供的特性，实现和使用起来更加方便流畅，甚至都感觉不到 ViewHolder 这种特殊机制的存在。

```kotlin
fun <T : View> View.findViewOften(viewId: Int): T {
    var viewHolder: SparseArray<View> = tag as? SparseArray<View> ?: SparseArray()
    tag = viewHolder
    var childView: View? = viewHolder.get(viewId)
    if (null == childView) {
        childView = findViewById(viewId)
        viewHolder.put(viewId, childView)
    }
    return childView as T
}
```

这里实现了一个 View 的扩展函数 `findViewOften(viewId: Int)` 意味着在需要频繁寻找一个视图的子视图的情况下使用，这样我们在 Item 中就可以这样写了。

```
val subTitle: TextView = convertView.findViewOften(R.id.list_item_subtitle)
subTitle.text = itemData.subTitle
```

由于 Kotlin 提供类型推断功能，所以 `findViewOften` 的返回值不用手动转换或者手动指定泛型类型。

利用 Kotlin 的语言特性，为 View 扩展一个方法，从此再也不用繁琐的定义 Viewholder 了，使用的时候也是如此的顺畅，从此再也不必记得什么 ViewHolder 了。

PS: 该方法在 [AndroidExtension](https://github.com/KotlinThree/AndroidExtension) 已经提供封装，这个库里面还封装了一些其他方法，也蛮好用的，不过这个库还没有正式发布。

## RecycleView 的 ViewHolder

最后，不得不提一下在 RecycleView 应该怎么办，因为在 RecycleView 的机制里面，在创建 Item 的 View 的时候，必须创建一个 RecyclerView.ViewHolder 并且返回。对于我们上面那么完美的封装， Google 这明显是在帮倒忙，还好这忙虽然帮倒了，不过还不至于无法挽回。

如果大家在使用 RecycleView 还想使用本文提供的方法的话，可以参考我下面的方式实现。提供一个 RecyclerView.ViewHolder 默认实现类，该类提供一个通过 `id` 获取视图的方法，在创建 Item 的 View 的时候默认都返回这个类的实例。

```
class MyViewHolder(val convertView: View) : RecyclerView.ViewHolder(convertView) {
    	fun <T : View> findView(viewId: Int): T {
        return convertView.findViewOften(viewId)
    }
}	
```

如果不想 `MyViewHolder` 的外部有不需要的依赖，可以将 `findViewOften` 直接实现在 `MyViewHolder` 里面。


## 欢迎大家关注我们的公众号

![](http://7xpox6.com1.z0.glb.clouddn.com/qrcode_for_gh_b2ad0581a6c4_430.jpg?imageView2/2/w/320) 

## 参考资料

- [ViewHolder](https://developer.android.com/training/improving-layouts/smooth-scrolling.html#ViewHolder)
- [Java Code Examples for android.util.SparseArray](http://www.programcreek.com/java-api-examples/android.util.SparseArray)
- [AndroidExtension](https://github.com/KotlinThree/AndroidExtension)
