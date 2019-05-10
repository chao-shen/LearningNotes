# 事件执行顺序(dispatchTouchEvent,setOnTouchListener, onTouchEvent,setOnclickListener 执行顺序问题)

# 操作流程
## 先自定义一个Button，在它的dispatchTouchEvent和onTouchEvent方法中加入log
```
    @Override
    public boolean dispatchTouchEvent(MotionEvent event) {
        System.out.println("chao......dispatchTouchEvent");
        return super.dispatchTouchEvent(event);
    }

     @Override
    public boolean onTouchEvent(MotionEvent event) {
        System.out.println("chao......onTouchEvent");
        return super.onTouchEvent(event);
    }
```
## 放进布局后在Activity中设置回调
```
 view.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                System.out.println("chao......setOnClickListener");
            }
        });

        view.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                System.out.println("chao......setOnTouchListener");
                return false;
            }
        });
```
## 在button上点击一下，日志信息为
![image](https://img-blog.csdnimg.cn/20190510221043390.png "")

首先执行dispatchTouchEvent，OnTouchListener，之后为onTouchEvent，最后才执行onClickListener内的方法，至于为什么OnTouchListener和onTouchEvent执行了两次，是因为在DOWN和UP时两个方法都被调用，至于onClickListener则只在UP的时候调用

我们接着看dispatchTouchEvent方法，从上面我们可以知道如果有的话OnTouchListener是比onTouchEvent先执行的，当然前提是OnTouchListener返回false，即OnTouchListener并没有处理事件

在View的onTouchEvent方法中，如果判断事件为MotionEvent.ACTION_UP时，则会调用performClick，而在performClick中则会回调mOnClickListener的onClick方法，即点击事件被回调，同时直接返回true
```
public boolean onTouchEvent(MotionEvent event) {
        ......
        switch (action) {
             case MotionEvent.ACTION_UP:
                  ......
                  performClick();
                  ......
                  break;
        ......
    }

public boolean performClick() {
        final boolean result;
        final ListenerInfo li = mListenerInfo;
        if (li != null && li.mOnClickListener != null) {
            playSoundEffect(SoundEffectConstants.CLICK);
            li.mOnClickListener.onClick(this);
            result = true;
        } else {
            result = false;
        }
        sendAccessibilityEvent(AccessibilityEvent.TYPE_VIEW_CLICKED);
        return result;
    }
```

### 如果setOnTouchListener返回true
```
view.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                System.out.println("chao......setOnTouchListener");
                return true;
            }
        });
```
### 再次单击button，日志信息为
![image](https://img-blog.csdnimg.cn/20190510222205915.png "")

因为在dispatchTouchEvent直接return true，没有走onTouchEvent方法，所以就没有走setOnTouchListener方法，归类如下

![image](https://img-blog.csdnimg.cn/20190510222628327.png "")

# 参考

[安卓OnTouchListener，onTouchEvent，onClickListener执行顺序](https://blog.csdn.net/xw13782513621/article/details/76648557)