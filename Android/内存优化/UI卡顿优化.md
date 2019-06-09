# UI卡顿优化
## Overdraw过度绘制

* 用手机中的GPU选项，就可以查看overdraw的情况，有蓝色，淡绿色，淡红色，深红色，目标是减少红色，尽量出现蓝色，原因是ui布局中有大量重叠的部分，可以通过viewstub实现，需要的时候按需加载，使用include 和merge标签减少复用布局而产生的布局嵌套。

* 还有的是非必要重叠的背景，比如activity有个背景，它内部的layout也有个背景，layout中的view还有个背景，这样的话，就会出现红色，只要去掉没必要的背景就可以减小ui过度绘制情况

## BlockCanary原理

## 简介
BlockCanary是国内开发者MarkZhai开发的一套性能监控组件,主要通过监控Handler中的dispatchMessage过程所消耗的时间是否超过阀值来判断是否发生卡顿。超过阀值之后,BlockCanary就会将一些必要的log日志输出.输出的展现方法类似LeakCanary

## 原理
界面卡顿主要是因为消息分发处理的不及时导致的,Android的消息分发机制主要是由Message/Looper/Handler。由于主线程只存在一个Looper,并且Android系统所有更新UI的操作都是在主线程里面执行的。因此所有的UI操作都会经过主线程的Looper消息循环。

## 注意
能不能检测到卡顿主要看对应的事件经不经过Handler分发,在issues中,有人提到用adb input keyevent E的方法让主线程休眠30s,这时BlockCanary就不能检测出ANR的问题,因为模拟按键缺少Handler的分发。
