# FlexboxLayout介绍
## FlexboxLayout是什么
* FlexboxLayout是一个Android平台上可伸缩的布局。可以简单、快捷的实现复杂布局
* FlexboxLayout可以理解成一个高级版的LinearLayout，因为两个布局都把子view按顺序排列。两者之间最大的差别在于FlexboxLayout具有换行的特性。

## FlexboxLayout支持的属性介绍
### FlexboxLayout 支持的属性
#### flexDirection
flexDirection属性决定了主轴的方向，即FlexboxLayout里子Item的排列方向，有以下4种取值：

* row (default)： 默认值，主轴为水平方向，从左到右。
* row_reverse：主轴为水平方向，起点在有端，从右到左。
* column：主轴为竖直方向，起点在上端，从上到下。
* column_reverse：主轴为竖直方向，起点在下端，从下往上。

#### flexWrap
flexWrap 这个属性决定Flex 容器是单行还是多行，并且决定副轴（与主轴垂直的轴）的方向。可能有以下3个值：

* noWrap (default): 不换行，一行显示完子元素。
* wrap: 按正常方向换行。
* wrap_reverse: 按反方向换行。

#### justifyContent
justifyContent 属性控制元素主轴方向上的对齐方式，有以下6种取值：

* flex_start (default): 默认值，左对齐
* flex_end: 右对齐
* center: 居中对齐
* space_between: 两端对齐，中间间隔相同
* space_around: 每个元素到两侧的距离相等
* space_evenly: 每个元素之间的距离相等

#### alignItems
alignItems 属性控制元素在副轴方向的对齐方式，有以下5种取值：

* stretch (default) ：默认值，如果item没有设置高度，则充满容器高度
* flex_start：顶端对齐
* flex_end：底部对齐
* center：居中对齐
* baseline：第一行内容的的基线对齐

![image](https://upload-images.jianshu.io/upload_images/3513995-963e8f4840db49b8.png?imageMogr2/auto-orient/strip|imageView2/2/w/1166/format/webp "")

#### alignContent
alignContent 属性控制多根轴线的对齐方式(也就是控制多行，如果子元素只有一行，则不起作用)，有一下6种取值：

* stretch (default): 默认值，充满交叉轴的高度（测试发现，需要alignItems 的值也为stretch 才有效）。
* flex_start: 与交叉轴起点对齐。
* flex_end: 与交叉轴终点对齐。
* center: 与交叉轴居中对齐。
* space_between: 交叉轴两端对齐，中间间隔相等。
* space_around: 到交叉轴两端的距离相等。

#### showDividerHorizontal
showDividerHorizontal 控制显示水平方向的分割线，有一下4种取值,可以是中的一个或者多个
* none(default) | beginning | middle | end

#### dividerDrawableHorizontal
* dividerDrawableHorizontal 设置水平方向的分割线。

#### showDividerVertical
showDividerVertical 控制显示垂直方向的分割线，有一下4种取值,可以是中的一个或者多个
* none(default) | beginning | middle | end

#### dividerDrawableVertical
* dividerDrawableVertical 设置子元素垂直方向的分割线。

#### showDivider
showDivider 控制显示水平和垂直方向的分割线，有一下4种取值,可以是中的一个或者多个
* none(default)| beginning | middle | end

#### dividerDrawable
* dividerDrawable 设置水平和垂直方向的分割线

### FleboxLayout子元素支持的属性介绍
#### layout_order(int)
layout_order 属性可以改变子元素的排列顺序，默认情况下，FlexboxLayout子元素的排列是按照xml文件中出现的顺序。默认值为1，值越小排在越靠前

#### layout_flexGrow(float)
layout_flexGrow 子元素的放大比例， 决定如何分配剩余空间（如果存在剩余空间的话），默认值为0,不会分配剩余空间，如果有一个item的 layout_flexGrow 是一个正值，那么会将全部剩余空间分配给这个Item,如果有多个Item这个属性都为正值，那么剩余空间的分配按照layout_flexGrow定义的比例（有点像LinearLayout的layout_weight属性）

#### layout_flexShrink(float)
layout_flexShrink:子元素缩小比例，当空间不足时，子元素需要缩小（设置了换行则无效），默认值为1，如果所有子元素的layout_flexShrink 值为1,空间不足时，都等比缩小，如果有一个为0，其他为1，空间不足时，为0的不缩小，负值无效。

#### layout_alignSelf
layout_alignSelf 属性可以给子元素设置对齐方式，上面讲的alignItems属性可以设置对齐，这个属性的功能和alignItems一样，只不过alignItems作用于所有子元素，而 layout_alignSelf 作用于单个子元素。默认值为auto, 表示继承alignItems属性，如果为auto以外的值，则会覆盖alignItems属性。有以下6种取值：

* auto (default)
* flex_start
* flex_end
* center
* baseline
* stretch

#### layout_flexBasisPercent (fraction)
layout_flexBasisPercent的值为一个百分比，表示设置子元素的长度为它父容器长度的百分比，如果设置了这个值，那么通过这个属性计算的值将会覆盖layout_width或者layout_height的值。但是需要注意，这个值只有设置了父容器的长度时才有效（也就是MeasureSpec mode 是 MeasureSpec.EXACTLY）。默认值-1。

#### layout_minWidth / layout_minHeight (dimension)
强制限制 FlexboxLayout的子元素（宽或高）不会小于最小值，不管layout_flexShrink这个属性的值为多少，子元素不会被缩小到小于设置的这个最小值。

#### layout_maxWidth / layout_maxHeight (dimension)
这个和上面的刚好相反，强制限制FlexboxLayout子元素不会大于这个最大值, 不管layout_flexGrow的值为多少，子元素不会被放大到超过这个最大值。

#### layout_wrapBefore
layout_wrapBefore 属性控制强制换行，默认值为false,如果将一个子元素的这个属性设置为true，那么这个子元素将会强制换行。这个属性在flex_wrap 非nowrap值生效。

## FlexboxLayout注意点
FlexboxLayout是没有考虑View回收的FlexboxLayout只适用于少量Item的场景，google提供了FlexboxlayoutManager，它是支持View回收的

![image](https://upload-images.jianshu.io/upload_images/3513995-b21a16b790495f09.png?imageMogr2/auto-orient/strip|imageView2/2/w/766/format/webp "")

### 用法
```
RecyclerView recyclerView = (RecyclerView) context.findViewById(R.id.recyclerview);
FlexboxLayoutManager layoutManager = new FlexboxLayoutManager(context);
layoutManager.setFlexDirection(FlexDirection.COLUMN);
layoutManager.setJustifyContent(JustifyContent.FLEX_END);
recyclerView.setLayoutManager(layoutManager);
```
# 参考
[FlexboxLayout基本使用](https://juejin.im/entry/59980994f265da249600b3e7)




