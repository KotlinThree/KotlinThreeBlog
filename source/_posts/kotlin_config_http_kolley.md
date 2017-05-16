title: Kotlin 实现配置化网络请求
date: 2016-06-13 20:23:05
tags: 
- Kotlin
- Android
- 网络请求

thumbnail: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-156658571.jpg?imageView2/1/w/200
banner: http://7xpox6.com1.z0.glb.clouddn.com/image/stock-photo-156658571.jpg?imageView2/1/w/1024/h/460 

---


Kotlin官方提供一个DSL的典型应用场景，[Anko](https://github.com/Kotlin/anko)致力直接用Kotlin配置页面布局和视图的属性。将布局文件代码化能够带来许多如类型安全、解析效率、代码重用等好处，而`Anko`让代码布局和XML一样简洁清晰。

<!-- more -->

受到`Anko`的启发，让我萌生了把`Android`中网络请求纷繁复杂配置信息也封装成配置化方式，实现如下方式的网络请求。
```kotlin
Http.get {
    url = "http://api.openweathermap.org/data/2.5/weather"
    headers {
        "Content-Type" - 'application/json'
        "pragma-token" - '33162acxxxxxx5032ad21e0e79ff70d'
    }
    params {
        "q" - "shanghai"
        "appid" - "d7a98cf22463b1c0c3df4adfe5abbc77"
    }
    onSuccess { bytes ->
        // handle data
    }
    onFail { error ->
        // handle error
    }
}
```

目前该框架已经完成，后面还会继续完善，项目地址[Kolley](https://github.com/ohmerhe/Kolley)

奔着这个目标，我把之前自己简单封装的Volley库翻出来，用Kotlin重新封装一下。经过分析总体过程大概如下：

- 基础代码转Kotlin
- 重定义原子Request
- Request构造配置化
- 提供RESTful方法

## 基础代码转Kotlin

之前的框架是参考[android-async-http](https://github.com/loopj/android-async-http)做的封装，用`okhttp`作为网络请求引擎，图片请求缓存模块使用的`jakewharton`提供的`disklrucache`，这两块都可以复用，先将这部分代码直接转成`Kotlin`实现。

这不需要花太多的功夫，将java代码复制过来以后，直接使用Android Studio的快速转换功能，转换后可能会有一些语法上的错误，稍微处理一下就可以了，得到类似的内容。

```kotlin
class OkHttpStack @JvmOverloads constructor(client: OkHttpClient = OkHttpClient()) : HurlStack() {
    private val mFactory: OkUrlFactory
    init {
        mFactory = OkUrlFactory(client)
    }
    @Throws(IOException::class)
    override fun createConnection(url: URL): HttpURLConnection {
        return mFactory.open(url)
    }
}
```

## 重定义原子Request

需要在Volley提供的`Request`基础上继承一个`BaseRequest`预处理一些信息，如params。

```kotlin
class ByteRequest(method: Int, url: String, errorListener: Response.ErrorListener? = Response.ErrorListener {})
: BaseRequest<ByteArray>(method, url, errorListener) {
    override fun parseNetworkResponse(response: NetworkResponse?): Response<ByteArray>? {
        return Response.success(response?.data, HttpHeaderParser.parseCacheHeaders(response))
    }
}
abstract class BaseRequest<D>(method: Int, url: String, errorListener: Response.ErrorListener? = Response.ErrorListener {})
: Request<D>(method, url, errorListener) {
    protected val DEFAULT_CHARSET = "UTF-8"
    internal var _listener: Response.Listener<D>? = null
    protected val _params: MutableMap<String, String> = HashMap() // used for a POST or PUT request.
    /**
     * Returns a Map of parameters to be used for a POST or PUT request.
     * @return
     */
    public override fun getParams(): MutableMap<String, String> {
        return _params
    }
    override fun deliverResponse(response: D?) {
        _listener?.onResponse(response)
    }
    protected fun log(msg: String) {
        if (BuildConfig.DEBUG) {
            Log.d(this.javaClass.simpleName, msg)
        }
    }
}
```

## Request构造配置化

上一步封装的`Request`必须在构造器中提供一些参数，并且像`Listener`这样的参数不能直接传递表达式，为配置化调用的封装提供了一定的困难。需要重新封装一个`Request`构造器，再在最后交给执行队列的时候创建真正的`Request`传递给它，这样让所有网络请求需要的配置信息都可以很方便的构造。

```kotlin
open class BaseRequestWapper() {
    internal lateinit var _request: ByteRequest
    var url: String = ""
    var method: Int = Request.Method.GET
    private var _start: (() -> Unit) = {}
    private var _success: (ByteArray) -> Unit = {}
    private var _fail: (VolleyError) -> Unit = {}
    private var _finish: (() -> Unit) = {}
    protected val _params: MutableMap<String, String> = HashMap() // used for a POST or PUT request.
    protected val _headers: MutableMap<String, String> = HashMap()
    var tag: Any? = null
    fun onStart(onStart: () -> Unit) {
        _start = onStart
    }
    fun onFail(onError: (VolleyError) -> Unit) {
        _fail = onError
    }
    fun onSuccess(onSuccess: (ByteArray) -> Unit) {
        _success = onSuccess
    }
    fun onFinish(onFinish: () -> Unit) {
        _finish = onFinish
    }
    fun params(makeParam: RequestPairs.() -> Unit) {
        val requestPair = RequestPairs()
        requestPair.makeParam()
        _params.putAll(requestPair.pairs)
    }
    fun headers(makeHeader: RequestPairs.() -> Unit) {
        val requestPair = RequestPairs()
        requestPair.makeHeader()
        _headers.putAll(requestPair.pairs)
    }
    fun excute() {
        var url = url
        if (Request.Method.GET == method) {
            url = getGetUrl(url, _params) { it.toQueryString() }
        }
        _request = ByteRequest(method, url, Response.ErrorListener {
            _fail(it)
            _finish()
        })
        _request._listener = Response.Listener {
            _success(it)
            _finish()
        }
        if (tag != null) {
            _request.tag = tag
        }
        Http.getRequestQueue().add(_request)
        _start()
    }
    private fun getGetUrl(url: String, params: MutableMap<String, String>, toQueryString: (map: Map<String, String>) ->
    String): String {
        return if (params == null || params.isEmpty()) url else "$url?${toQueryString(params)}"
    }
    private fun <K, V> Map<K, V>.toQueryString(): String = this.map { "${it.key}=${it.value}" }.joinToString("&")
}
```

代码中将网络请求需要的所有信息全部包装了一层，这样在调用的时候就可以很方便的逐个设置每个参数（当然会有一些默认值），最后在`excute()`方法中全部设置给真正的`Request`。这个封装保证了下面的调用方式：

```kotlin
url = "http://api.openweathermap.org/data/2.5/weather"
params {
    "q" - "shanghai"
    "appid" - "d7a98cf22463b1c0c3df4adfe5abbc77"
}
onSuccess { bytes ->
    // handle data
}
...
```

PS：上面`params`是的书写方式，使用了`Kotlin`的操作符重载功能，具体实现可以下载[源码](https://github.com/ohmerhe/Kolley)看下。

## 提供RESTful方法

实现到上一步，已经准备的差不多了，接下来还需要最后一步，提供RESTful请求方法。

```kotlin
object Http {
    private var mRequestQueue: RequestQueue? = null
    fun init(context: Context) {
        // Set up the network to use OKHttpURLConnection as the HTTP client.
        // getApplicationContext() is key, it keeps you from leaking the
        // Activity or BroadcastReceiver if someone passes one in.
        mRequestQueue = Volley.newRequestQueue(context.applicationContext, OkHttpStack(OkHttpClient()))
    }
    fun getRequestQueue(): RequestQueue {
        return mRequestQueue!!
    }
    val request: (Int, BaseRequestWapper.() -> Unit) -> Request<ByteArray> = { method, request ->
        val baseRequest = BaseRequestWapper()
        baseRequest.method = method
        baseRequest.request()
        baseRequest.excute()
        baseRequest._request
    }
    val post = request.partially1(Request.Method.POST)
    val put = request.partially1(Request.Method.PUT)
    val delete = request.partially1(Request.Method.DELETE)
    val head = request.partially1(Request.Method.HEAD)
    val options = request.partially1(Request.Method.OPTIONS)
    val trace = request.partially1(Request.Method.TRACE)
    val patch = request.partially1(Request.Method.PATCH)
}
```

上面的`request: (Int, BaseRequestWapper.() -> Unit) -> Request<ByteArray>`方法为网络请求提供了入口、保证了配置化代码都可以在`{}`中调用、完成了真正网络请求添加到执行队列。用户可以通过`http.requset(method){}`方式发起各种请求。

`val get = request.partially1(Request.Method.GET)`等提供了RESTful方法的封装，实现`Http.get{}`的方便调用。

## 后续

关于图片请求模块的实现，其实也是异曲同工，虽然更加复杂一点，但是具体思路是一样的。有兴趣的可以下载[源码](https://github.com/ohmerhe/Kolley)查看实现，也欢迎提交代码。

图片请求的方式

```kotlin
Image.display {
    url = "http://7xpox6.com1.z0.glb.clouddn.com/android_bg.jpg"
    imageView = mImageView
    options {
        // these values are all default value , you do not need specific them if you do not want to custom
        imageResOnLoading = R.drawable.default_image
        imageResOnLoading = R.drawable.default_image
        imageResOnFail = R.drawable.default_image
        decodeConfig = Bitmap.Config.RGB_565
        scaleType = ImageView.ScaleType.CENTER_CROP
        maxWidth = ImageDisplayOption.DETAULT_IMAGE_WIDTH_MAX
        maxHeight = ImageDisplayOption.DETAULT_IMAGE_HEIGHT_MAX
    }
}
```

```kotlin
Image.load {
    url = "http://7xpox6.com1.z0.glb.clouddn.com/android_bg.jpg"
    options {
        scaleType = ImageView.ScaleType.CENTER_CROP
        maxWidth = ImageDisplayOption.DETAULT_IMAGE_WIDTH_MAX
        maxHeight = ImageDisplayOption.DETAULT_IMAGE_HEIGHT_MAX
    }
    onSuccess { bitmap ->
        _imageView2?.setImageBitmap(bitmap)
    }
    onFail { error ->
        log(error.toString())
    }
}
```


## 参考资料

- [Anko](https://github.com/Kotlin/anko)
- [Kotlin Refrence](https://kotlinlang.org/docs/reference/)
- [Volley](https://developer.android.com/training/volley/index.html)
- [OKHttp](http://square.github.io/okhttp)
