# LruCache原理
LRU (Least Recently Used) 即最近最少使用算法,LruCache是一个内存层面的缓存，如果想要进行本地磁盘缓存，推荐使用DiskLruCache

## 使用 
一般重写构造和sizeOf方法即可，分别定义为lru分配的总内存（通常是最大内存的八分之一）和每个节点的内存大小

## 原理
1.trimToSize内部维护个死循环，内部代码逻辑为

* 当size 小于等于maxSize时，直接退出循环
* 当size 大于maxSize时，则会移除LinkedHashMap第一个位置元素，直至满足size 小于等于maxSize时再退出循环

LruCache如何控制及更新缓存的大小的，主要是在线程同步块里对size字段进行更新,然后根据size字段和maxSize字段的大小关系来修剪节点

2.如何做到最近最少使用呢？ 

LinkedHashMap 帮我们做到最近最少使用的排序。

## 总结

* LruCache内部又维护了一个双向链表结构，当我们有访问操作时候，被访问节点会移到链表结尾；

* 当通过trimToSize判断分配内存已满时，则删除链表中第一个元素，直到当前占用的内存小等于刚开始分配的总内存

* 在Lrucache的put、get和remove方法中，对集合操作时使用了synchronized关键字，来保证线程安全；

# 参考
[Android LruCache源码分析](https://www.jianshu.com/p/ef22974673ea)
