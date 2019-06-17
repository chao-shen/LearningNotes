# LeakCanary原理

## 简介
LeakCanary是一款开源的内存泄漏检查工具，在项目中，可以使用它来检测Activity是否能够被GC及时回收

## 原理
监测机制利用了Java的WeakReference和ReferenceQueue，通过将Activity包装到WeakReference中，被WeakReference包装过的Activity对象如果被回收，该WeakReference引用会被放到ReferenceQueue中，通过监测ReferenceQueue里面的内容就能检查到Activity是否能够被回收

1、首先通过removeWeaklyReachablereference来移除已经被回收的Activity引用
```
private void removeWeaklyReachableReferences() {
 while ((ref = (KeyedWeakReference) queue.poll()) != null) {
        retainedKeys.remove(ref.key);//根据key值移除set集合中存在的对应key
    }
}
```

2、通过gone(reference)判断当前弱引用对应的Activity是否已经被回收，如果已经回收说明activity能够被GC，直接返回即可。（即retainedKeys集合中没有包含引用对象对应的key，它是个set集合，在刚开始RefWatch.watch方法里添加了activity索引的key，key是通过randomUUID()生成的，在RefWatcher的构造方法里创建了retainedKeys 和ReferenceQueue）
```
private boolean gone(KeyedWeakReference reference) {
    return !retainedKeys.contains(reference.key);
}
```

3、如果Activity没有被回收，调用GcTigger.runGc方法运行GC，GC完成后（GC后有个100ms的延迟，让弱引用有足够时间放入到引用队列中）在运行第1步，然后运行第2步判断Activity是否被回收了，如果这时候还没有被回收，那就说明Activity可能已经泄露。
```
 public void runGc() {
        Runtime.getRuntime().gc();//调用Runtime.gc()来执行GC操作
        enqueueReferences();//等待100ms中，等待弱引用对象进入引用队列中
        System.runFinalization();//执行对象的finalize()方法
    }

     private void enqueueReferences() {
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            throw new AssertionError();
        }
    }
```

4、通过HeapAnalyzerService.runAnalysis进行分析内存文件分析

5、最后通过DisplayLeakService进行内存泄漏的展示

（其实 就是 onDestory的时候 把Activity实例 用弱引用 和 RQ绑定起来了，然后 被销毁了 就会出现在RQ里，如果没出现就GC一次 GC一次后 还没在RQ里 就是内存泄露了）


## 注意
在 LeakCanary.install(this);方法中的buildAndInstall里只注册了ActivityRefWatcher，说明只检测Activity的泄漏问题

* 要想检测其他页面例如fragment，则需要Application加上RefWatcher，然后在对应fragment页面中onDestroy中加入

    RefWatcher refWatcher = MyApplication.getRefWatcher(this);

    refWatcher.watch(this);

* 检查bitmap泄漏（包括所有检测内存泄漏，最终都是调用RefWatcher.watch来检测的）

    RefWatcher refWatcher = MyApplication.getRefWatcher(this);

    refWatcher.watch(bitmap);


其中watch中参数this代表要检测的对象

[LeakCanary原理解析](https://www.jianshu.com/p/261e70f3083f)

[LeakCanary原理浅析](https://www.jianshu.com/p/70de36ea8b31)