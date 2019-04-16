# 内存问题
* 内存抖动：锯齿状、GC导致卡顿
* 内存泄漏：可用内存减少、频繁GC
* 内存溢出：OOM、程序异常

# 工具选择
* Memory Profiler
* Memory Analyzer
* LeakCanary

# Memory Profiler用法
## 堆转储-查看当前内存分配
![image](https://img-blog.csdnimg.cn/20190415213411918.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYW9fc2hlbg==,size_16,color_FFFFFF,t_70 "")

## 点击后进入如下页面，例如查看bitmap使用情况
![image](https://img-blog.csdnimg.cn/20190415214519596.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYW9fc2hlbg==,size_16,color_FFFFFF,t_70 "")
在图中可看到堆中所有的Bitmap对象,列表上显示了Bitmap所占的内存大小,点击后可以查看对应是预览图

    Shallow size就是对象本身占用内存的大小，不包含其引用的对象。
    Retained size是该对象自己的shallow size，加上从该对象能直接或间接访问到对象的shallow size之和。换句话说，retained size是该对象被GC之后所能回收到内存的总和。

# 内存抖动介绍
## 定义
内存频繁分配和回收导致内存不稳定
表现为频繁GC,内存曲线呈锯齿状

## 内存抖动的危害
内存抖动是因为频繁创建对象,会导致内存不足以及内存碎片(不连续),应用卡顿

## 内存抖动定位技巧（可以考虑用Memory Profiler更直观查看）
循环或者频繁调用的地方