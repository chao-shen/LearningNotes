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

## 修复工作原理
服务端做dex差量，将差量包下发到客户端，在ART模式的机型上本地跟原apk中的classes.dex做merge，merge成为一个新的merge.dex后将merge.dex插入pathClassLoader的dexElement，原理类同Q-Zone

![image](https://images2018.cnblogs.com/blog/823551/201803/823551-20180311132842593-173785053.png "")

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