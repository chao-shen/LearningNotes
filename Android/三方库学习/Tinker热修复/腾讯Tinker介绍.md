# 热更新流程

1.线上检测到严重的crash

2.拉出bug修复分支，并在分支上修复问题

3.构建生成补丁

4.App通过推送或者主动拉取补丁文件

5.将bug分支代码合并到master上

# 腾讯Tinker介绍
对比其他热修复库：

![image](https://images2017.cnblogs.com/blog/967362/201710/967362-20171019165934131-408638073.png "")

简述用法：gradle配置环境，通过推送将补丁包地址推送到客户端，然后下载到指定目录，再后app在加载该补丁包

生成补丁包命令：
java -jar  tinker-patch-cli-1.7.7.jar

![image](https://img-blog.csdn.net/20171224202058893 "")

在output文件夹中看到tinker为我们生成的apk文件，patch_signed.apk(签名版的)为补丁文件

注意补丁包中的classes.dex非常小，就是补丁的dex文件

# 原理
DexClassLoader 主要加载非系统级别（用户级别）的文件，比如jar，apk，dex文件

PathClassLoader 只能加载系统中已经安装的apk


![image](https://img-blog.csdnimg.cn/20190517085622272.png "")

## dex修复工作原理（分两个步骤讲解）
tinker将old.apk和new.apk做了diff，拿到patch.dex，然后将patch.dex与本机中apk的classes.dex做了合并，生成新的fix_classes.dex，运行时通过反射将合并后的dex文件放置在加载的dexElements数组的前面。

![image](https://images2018.cnblogs.com/blog/823551/201803/823551-20180311132842593-173785053.png "")

### 第一步-如何加载patch
根据不同的系统版本，去反射处理dexElements

```
  ClassLoader classLoader = loader;
        if (Build.VERSION.SDK_INT >= 24) {
            classLoader = AndroidNClassLoader.inject(loader, application);
        }
        //because in dalvik, if inner class is not the same classloader with it wrapper class.
        //it won't fail at dex2opt
        if (Build.VERSION.SDK_INT >= 23) {
            V23.install(classLoader, files, dexOptDir);
        } else if (Build.VERSION.SDK_INT >= 19) {
            V19.install(classLoader, files, dexOptDir);
        } else if (Build.VERSION.SDK_INT >= 14) {
            V14.install(classLoader, files, dexOptDir);
        } else {
            V4.install(classLoader, files, dexOptDir);
        }

```
虽然版本不一样，处理方式所有不同，但大体分为5个步骤：

1.反射找到PathClassLoader（BaseDexClassLoader）对象中的pathList对象

2.根据pathList对象找到其中的makeDexElements方法，传入patch相关的对应的实参，返回Element[]对象

3.拿到pathList对象中原本的dexElements方法

4.步骤2与步骤3中的Element[]数组进行合并，将patch相关的dex放在数组的前面

5.最后将合并后的数组，设置给pathList

### 第二步-如何合并patch
* 首先解析了meta里面的信息，meta中包含了patch中每个dex的相关数据。然后通过ApplicationInfo拿到sourceDir，其实就是本机apk的路径；根据meta中的信息开始遍历，其实就是取出对应的dex文件，最后通过patchDexFile方法对两个dex文件做合并。

* patchDexFile方法内部实现，通过ZipFile拿到其内部文件的InputStream，其实就是读取本地apk对应的dex文件，以及patch中对应dex文件，对二者的通过executeAndSaveTo方法进行合并至patchedDexFile，即patch的目标私有目录，patchedDexFile这个文件就是合并后的dex文件（合并算法没有了解）

## 资源修复工作原理（分两个步骤讲解）


## 优点
* 支持动态下发代码

* 支持替换So库以及资源

## 缺点
* 不能即时生效，需要下次启动

## Tinker已知问题
* Tinker不支持修改AndroidManifest.xml
* 不支持部分三星android-21机型，加载补丁时会主动抛出"TinkerRuntimeException:checkDexInstall failed"；
* Dex合并内存消耗在vm head上，容易OOM，最后导致合并失败。

# 注意

1.multiDexEnabled true 必须设置为true，这样支持多个dex文件

2.之所以通过注解生成application，是因为在apk安装的时候，虚拟机会将dex优化成odex后才拿去执行。在这个过程中会对所有class一个校验。
校验方式：假设A类在它的static方法，private方法，构造函数，override方法中直接引用到B类。如果A类和B类在同一个dex中，那么A类就会被打上CLASS_ISPREVERIFIED标记，被打上这个标记的类不能引用其他dex中的类，否则就会报如下异常
```
java.lang.IllegalAccessError: Class ref in pre-verified class resolved to unexpected implementation
```

# 参考
[Android 热修复 Tinker接入及源码浅析](https://blog.csdn.net/lmj623565791/article/details/54882693)

[Android 热修复方案Tinker(三) Dex补丁加载](https://blog.csdn.net/l2show/article/details/53307523)