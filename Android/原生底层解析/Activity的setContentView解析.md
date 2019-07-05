# Activity的setContentView解析

* Activity里面的setContentView()实际上调用的是PhoneWindow的setContentView方法，然后再该方法里做了一个判断

  * 如果mContentParent等于空，调用installDecor()，在installDecor方法中初始化DecorView和mContentParent的，
  
  * 不为空则直接清除mContentParent所有子view，
  
  * 在setContentView()方法最后，将咱们自己的布局layoutId加入到mContentParent里

* 实例化DecorView，然后做一系列的判断然后去解析系统的资源layoutId文件，至于解析哪一个资源文件会做判断,比如有没有头部等等，把它解析加载到DecorView

* 我们自己通过setContentView设置的布局id其实是解析到mContentParent里面的，也就是那个id叫做android.R.id.content的FarmeLayout

![image](https://upload-images.jianshu.io/upload_images/4314397-a952a39dd0bba2d9.png "")

## DecorView布局层次（DecorView本身也是FrameLayout）

* DecorView的子布局有两个,一个LinearLayout，一个是statusbarbackground是一个view，状态栏;

* LinearLayout里边有两个子布局,第一个是个ViewStub,第二个是个FrameLayout

  * 其中ViewStub就是ActionBar,他会根据theme判断有没有使用ActionBar来决定是否引入真实ActionBar的布局;

  * 第二个FrameLayout,就是我们应用中真实使用布局的父布局

  
![image](https://img-blog.csdnimg.cn/20190706000336368.png "")

* 红色框是DecorView的两个子布局

* 绿色框是DecorView的子布局LinearLayout的子布局


# 参考
[插件式换肤框架搭建 - setContentView源码阅读](https://www.jianshu.com/p/531d1168b3ee)
