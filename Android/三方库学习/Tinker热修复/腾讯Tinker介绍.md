# 热更新流程

1.线上检测到严重的crash

2.拉出bug修复分支，并在分支上修复问题

3.构建生成补丁

4.App通过推送或者主动拉取补丁文件

5.将bug分支代码合并到master上

# 腾讯Tinker介绍
对比其他热修复库：

![image](https://images2017.cnblogs.com/blog/967362/201710/967362-20171019165934131-408638073.png "")

简述用法：gradle配置环境，每次启动app时检测有无补丁包，有的话，下载到指定目录，然后app在加载该补丁包

生成补丁包命令：
java -jar  tinker-patch-cli-1.7.7.jar

![image](https://img-blog.csdn.net/20171224202058893 "")

在output文件夹中看到tinker为我们生成的apk文件，patch_signed.apk(签名版的)为补丁文件

注意补丁包中的classes.dex非常小，就是补丁的dex文件

# 原理
DexClassLoader主要加载非系统级别（用户级别）的文件，比如jar，apk文件

PathClassLoader主要加载系统级别的文件，比如apk文件

![image](https://img-blog.csdnimg.cn/20190517085622272.png "")

## 修复工作原理
1.找到DexPathList类

2.利用反射在DexPathList的dexElements容器中添加补丁包，必须要在旧版本的dex Element元素前

3.通过循环加载我们的补丁包，完成最后的修复工作

# 注意

1.multiDexEnabled true 必须设置为true，这样支持多个dex文件

2.之所以通过注解生成application，是因为在apk安装的时候，虚拟机会将dex优化成odex后才拿去执行。在这个过程中会对所有class一个校验。
校验方式：假设A该类在它的static方法，private方法，构造函数，override方法中直接引用到B类。如果A类和B类在同一个dex中，那么A类就会被打上CLASS_ISPREVERIFIED标记，被打上这个标记的类不能引用其他dex中的类，否则就会报如下异常
```
java.lang.IllegalAccessError: Class ref in pre-verified class resolved to unexpected implementatio
```