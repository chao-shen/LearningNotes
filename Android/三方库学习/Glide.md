# Glide源码分析

`Glide.with(this).load(url).into(imageView);`

## with()解析
通过RequestManagerRetriever类的get()方法获取RequestManager对象，其中get参数可以Context参数，Activity参数，Fragment参数等等。实际上只有两种情况而已，即传入Application类型的参数，和传入非Application类型的参数。

### Application参数的情况
Glide.with()方法中传入的是一个Application对象，Application对象的生命周期即应用程序的生命周期，因此Glide并不需要做什么特殊的处理，它自动就是和应用程序的生命周期是同步的，如果应用程序关闭的话，Glide的加载也会同时终止

### 非Application参数的情况
不管你在Glide.with()方法中传入的是Activity、FragmentActivity、v4包下的Fragment、还是app包下的Fragment，最终的流程都是一样的，那就是会向当前的Activity当中添加一个隐藏的Fragment。用这个Fragment监听该页面的生命周期，该页面销毁，Fragment是可以监听到的，这样Glide就可以捕获这个事件并停止图片加载了

## load()解析
返回一个DrawableTypeRequest对象，它最主要的就是它提供了asBitmap()和asGif()这两个方法，其中DrawableTypeRequest的父类DrawableRequestBuilder提供很多对图片进行处理的方法，比如说placeholder()方法、error()方法、diskCacheStrategy()方法、override()方法等

## into解析
主要用作缓存，并把图片显示到控件上

# Glide缓存
两个模块，一个是内存缓存，一个是硬盘缓存
## 内存缓存
Glide默认开启内存缓存

如要禁用内存缓存

参考
```
Glide.with(this)
     .load(url)
     .skipMemoryCache(true)
     .into(imageView);
```

## 硬盘缓存
Glide默认开启硬盘缓存

如要禁用硬盘缓存

参考
```
Glide.with(this)
     .load(url)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);
```

### 内存缓存原理

获取内存缓存

loadFromCache()：LruCache算法  
使用缓存Key来从cache当中取值，cache对象就是在构建Glide对象时创建的LruResourceCache

loadFromActiveResources()：弱引用  
从activeResources这个HashMap当中取值。使用activeResources来缓存正在使用中的图片，可以保护这些图片不会被LruCache算法回收掉。

写入内存缓存

LruCache算法  
handleResultOnMainThread()中有两个方法，第一个acquire()、第二个release()，用一个acquired变量用来记录图片被引用的次数，调用acquire()方法会让变量加1，调用release()方法会让变量减1，当acquired变量大于0的时候，说明图片正在使用中，也就应该放到activeResources弱引用缓存当中。如果acquired变量等于0了，说明图片已经不再被使用了，会将缓存图片从activeResources中移除，然后再将它put到LruResourceCache当中。这样也就实现了正在使用中的图片使用弱引用来进行缓存，不在使用中的图片使用LruCache来进行缓存的功能。


弱引用

当图片加载完成之后EngineJob当中通过Handler发送一条消息将执行逻辑切回到主线程当中，从而执行handleResultOnMainThread()，EngineResource被put到了activeResources当中，也就是在这里写入的缓存

### 硬盘缓存原理
Glide默认情况下在硬盘缓存的就是转换过后的图片，我们通过调用diskCacheStrategy()方法则可以改变这一默认行为。

decodeFromCache()方法从硬盘缓存当中读取图片

decodeFromSource()来读取原始图片