# View相关知识
## view绘制流程
当一个应用启动时，会启动一个主 Activity，Android 系统会根据 Activity 的布局来对它进行绘制。绘制会从根视图 ViewRoot 的 performTraversals() 方法开始，从上到下遍历整个视图树，每个 View 控制负责绘制自己，而 ViewGroup 还需要负责通知自己的子 View 进行绘制操作。

## View 绘制中主要流程分为measure，layout， draw 三个阶段。
measure ：根据父 view 传递的 MeasureSpec 进行计算大小。

layout ：根据 measure 子 View 所得到的布局大小和布局参数，将子View放在合适的位置上。

draw ：把 View 对象绘制到屏幕上。

## MeasureSpec，主要分3类模式
UNSPECIFIED：不指定测量模式，父视图没有限制子视图的大小，子视图可以是想要的任何尺寸，通常用于系统内部，应用开发中很少使用到。

EXACTLY：精确测量模式，当该视图的 layout_width 或者 layout_height 指定为具体数值或者 match_parent 时生效，表示父视图已经决定了子视图的精确大小，这种模式下 View 的测量值就是 SpecSize 的值。

AT_MOST：最大值模式，当前视图的 layout_width 或者 layout_height 指定为 wrap_content 时生效，此时子视图的尺寸可以是不超过父视图运行的最大尺寸的任何尺寸。

## view绘制顺序（主要是draw()方法）
![image](https://img-blog.csdnimg.cn/2019060521490221.png "")

![image](https://img-blog.csdnimg.cn/20190606084037650.png "")

## 布局过程（测量阶段和布局阶段）

### 测量阶段
measure() 方法被父 View 调用，在 measure() 中做一些准备和优化工作后，调用  onMeasure() 来进行实际的自我测量。 onMeasure() 做的事，View 和 ViewGroup 不一样
* View：View 在 onMeasure() 中会计算出自己的尺寸然后保存

* ViewGroup：ViewGroup 在 onMeasure() 中会调用所有子 View 的 measure() 让它们进行自我测量，并根据子 View 计算出的期望尺寸来计算出它们的实际尺寸和位置（实际上 99.99% 的父 View 都会使用子 View 给出的期望尺寸来作为实际尺寸）然后保存。同时，它也会根据子 View 的尺寸和位置来计算出自己的尺寸然后保存

### 布局阶段
layout() 方法被父 View 调用，在 layout() 中它会保存父 View 传进来的自己的位置和尺寸，并且调用 onLayout() 来进行实际的内部布局。onLayout() 做的事， View 和 ViewGroup 也不一样

* View：由于没有子 View，所以 View 的 onLayout() 什么也不做
* ViewGroup：ViewGroup 在 onLayout() 中会调用自己的所有子 View 的 layout() 方法，把它们的尺寸和位置传给它们，让它们完成自我的内部布局

## 触摸反馈
1.重写 onTouchEvent()，在里面写上你的触摸反馈算法，并返回 true（关键是 ACTION_DOWN 事件时返回  true）。

2.如果是会发生触摸冲突的 ViewGroup，还需要重写 onInterceptTouchEvent()，在事件流开始时返回  false，并在确认接管事件流时返回一次 true，以实现对事件的拦截。

3.当子 View 临时需要阻止父 View 拦截事件流时，可以调用父 View 的  requestDisallowInterceptTouchEvent() ，通知父 View 在当前事件流中不再尝试通过onInterceptTouchEvent() 来拦截。


## 注意
1.onMeasure(int widthMeasureSpec, int heightMeasureSpec)的参数

是由mode+size两部分组成的。将int类型转化成二进制数字表示，他们都是32位的。前两位代表mode(测量模式)，后面30位才是他们的实际数值（size）。 

2.getMeasuredWidth()与getWidth()区别

getMeasureWidth()方法在measure()过程结束后就可以获取到，而getWidth()方法要在layout()过程结束后才能获取到。
getMeasureWidth()方法中的值是通过setMeasuredDimension()方法来进行设置的，而getWidth()方法中的值则是通过layout(left,top,right,bottom)方法设置的

3.setMeasuredDimension()提供的测量结果只是为布局提供建议，最终的取用与否要看layout()函数

4.疑问：在onLayout()中布局它所有的子控件。那它自己什么时候被布局呢？

在所有的控件的最顶部有一个ViewRoot，它才是所有控件的最终祖先结点，在它布局里，会调用它自己的一个layout()函数(不能被重载，代码位于View.java)：

```
public final void layout(int l, int t, int r, int b) {  
    boolean changed = setFrame(l, t, r, b); //设置每个视图位于父视图的坐标轴  
    if (changed || (mPrivateFlags & LAYOUT_REQUIRED) == LAYOUT_REQUIRED) {  
        if (ViewDebug.TRACE_HIERARCHY) {  
            ViewDebug.trace(this, ViewDebug.HierarchyTraceType.ON_LAYOUT);  
        }  
  
        onLayout(changed, l, t, r, b);//回调onLayout函数 ，设置每个子视图的布局  
    }  

```

在SetFrame(l,t,r,b)就是设置自己的位置，设置结束以后才会调用onLayout(changed, l, t, r, b)来设置内部所有子控件的位置。



## 小试牛刀(项目中圆角图片实现)
onDraw方法中，先定一个path，调用它addRoundRect方法来定义一个圆角矩形路径，然后调用canvas的clipPath方法裁剪
```
@Override
    protected void onDraw(Canvas canvas) {
        Path path = new Path();
        int w = getWidth();
        int h = getHeight();
        //这里对path添加一个圆角区域，这里一般需要将dp转换为pixel
        path.addRoundRect(new RectF(0,0,w,h), DensityUtils.dpToPx(20),DensityUtils.dpToPx(20), Path.Direction.CW);
        canvas.clipPath(path);//将Canvas按照上面的圆角区域截取
        super.onDraw(canvas);
    }
```


# 参考
[Android自定义控件三部曲文章索引](https://blog.csdn.net/harvic880925/article/details/50995268)