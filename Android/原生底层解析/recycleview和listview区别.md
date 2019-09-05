# recycleview和listview区别
## ListView回收机制

ListView为了保证Item View的复用，实现了一套回收机制，该回收机制的实现类是RecycleBin，他实现了两级缓存：

* View[] mActiveViews: 缓存屏幕上的View，在该缓存里的View不需要调用getView()。

* ArrayList[] mScrapViews;: 每个Item Type对应一个列表作为回收站，缓存由于滚动而消失的View，此处的View如果被复用，会以参数的形式传给getView()。

## RecyclerView回收机制

RecyclerView和ListView的回收机制非常相似，但是ListView是以View作为单位进行回收，RecyclerView是以ViewHolder作为单位进行回收。Recycler是RecyclerView回收机制的实现类，他实现了四级缓存：

* mAttachedScrap: 缓存在屏幕上的ViewHolder。
* mCachedViews: 缓存屏幕外的ViewHolder，默认为2个。ListView对于屏幕外的缓存都会调用getView()。
* mViewCacheExtensions: 需要用户定制，默认不实现。
* mRecyclerPool: 缓存池，多个RecyclerView共用。

## 缓存不同
### ListView获取缓存的流程：
![image](https://img-blog.csdn.net/2018051614072985 "")

### RecyclerView获取缓存的流程：
![image](https://img-blog.csdn.net/20180516140737261 "")

1). RecyclerView中mCacheViews(屏幕外)获取缓存时，是通过匹配pos获取目标位置的缓存，这样做的好处是，当数据源数据不变的情况下，无须重新bindView.而同样是离屏缓存，ListView从mScrapViews根据pos获取相应的缓存，但是并没有直接使用，而是重新getView（即必定会重新bindView）

2). ListView中通过pos获取的是view，即pos–>view；
RecyclerView中通过pos获取的是viewholder，即pos –> (view，viewHolder，flag)；
从流程图中可以看出，标志flag的作用是判断view是否需要重新bindView，这也是RecyclerView实现局部刷新的一个核心。

##  局部刷新
以RecyclerView中notifyItemRemoved(1)为例，最终会调用requestLayout()，使整个RecyclerView重新绘制，过程为：
onMeasure()-->onLayout()-->onDraw()

onLayout()为重点，核心函数为layoutChildren()；

### 其中，layoutChildren()流程图：
![image](https://blog-10039692.file.myqcloud.com/1502175847574_5862_1502175847713.png "")

当调用notifyItemRemoved时，会对屏幕内ItemView做预处理，修改ItemView相应的pos以及flag(流程图中红色部分)：
![image](https://blog-10039692.file.myqcloud.com/1502175883399_2814_1502175883495.png "")

当调用fill()中RecyclerView.getViewForPosition(pos)时，RecyclerView通过对pos和flag的预处理，使得bindview只调用一次.

# 参考
[深入列表(三),RecycleView ListView 缓存对比](https://blog.csdn.net/Yuequnchen/article/details/80336180)

[RecyclerView与ListView 对比浅析：缓存机制](https://www.cnblogs.com/ganchuanpu/p/8258459.html)

