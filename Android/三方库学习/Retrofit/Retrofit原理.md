# Retrofit介绍
Retrofit是一个Android网络框架。是一个对OKHttp框架的简单封装。所以其内部实现原理实际上也是基于OkHttp的请求方式的。Retrofit采用注解方式开发

# Retrofit使用
```
 new Retrofit.Builder()
                .baseUrl("https://commentinteract.cn/")
                .client(httpClient)
```
## 注意
需要注意，baseUrl不能以完整的URL作为参数传入，必须只传一个域（因为必须以”/”作为结尾，不然报错）

Retrofit还提供了一个注解@URL。只要在方法中声明一个参数，并加上@URL注解，就可以动态地将完整的URL作为请求的参数
```
    @GET
    Observable<InfoEntity> getUserInfo(@Url String addrUrl);
```
这样，无论BaseUrl传入什么参数，都不起作用，而是用getUserInfo的参数addrUrl作为请求的URL。

# Retrofit原理
使用了反射和代理的设计模式

1.Retrofit实例的构造，填充一些参数，比如callAdapterFactory、convertFactory，以及baseUrl，callFactory（与callAdapterFactory作区分，若不指定，则为一个默认的OkHttpClient，可以根据需要创建一个OkHttpClient，然后对这个OkHttpClient添加对应的属性，以实现不同的网络处理机制）。

2.Retrofit调用create创建一个代理实体，而代理关键部分在于创建一个ServiceMethod，ServiceMethod收集接口方法的参数类型以及对应的注解，组成一个有关网络请求的参数类型集合。

3.使用ServiceMethod实例和方法调用参数创建OkHttpCall，调用serviceMethod.callAdapter.adapt(okHttpCall)来构成一个Call对象，经过CallAdapterFactory的适配，返回。因此接口方法可以理解为最后获得这个Call对象（真实来说应该是OkHttpCall）。

4.Call对象调用enqueue实际使用的是OkHttp的RealCall的enqueue方法，因此Retrofit的Call对象可以看成是OkHttp的Call的一个封装。并且，在网络请求获得响应后，OkHttpCall还会对返回的信息进行转置，根据ConvertFactory定义的转置模式进行转换。

```
OkHttpClient httpClient = null;
        try {
            httpClient = new OkHttpClient.Builder().connectTimeout(12, TimeUnit.SECONDS)
                    .writeTimeout(12, TimeUnit.SECONDS)
                    .sslSocketFactory(getSSLSocketFactory(new ByteArrayInputStream(C.CCTVCN_CRT.getBytes())))
                    .hostnameVerifier(org.apache.http.conn.ssl.SSLSocketFactory.ALLOW_ALL_HOSTNAME_VERIFIER)
                    .readTimeout(12, TimeUnit.SECONDS)
                    .addInterceptor(publicParams)
                    .addInterceptor(logInterceptor)
                    .addInterceptor(mHeaderInterceptor)
                    .build();
        } catch (Exception e) {
            LogUtils.e(e);
        }
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(baseUrl)
                .client(httpClient)
                //增加返回值为Gson的支持(以实体类返回)
                .addConverterFactory(GsonConverterFactory.create())
                //增加返回值为Oservable<T>的支持
                .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
                .build();
```

# 参考文章
[Retrofit原理浅析](https://www.jianshu.com/p/cd69c75d053e)