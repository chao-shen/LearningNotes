# String 介绍

```
案例1：
String a="abc";
String b="abc";
System.out.println("a==b:"+(a==b));//true
System.out.println("a.equals(b):"+a.equals(b));//true


案例2：
String a = new String("abc");
String b = new String("abc");
System.out.println("a==b:" + (a == b));//false
System.out.println("a.equals(b):" + a.equals(b));//true
```

## 分析如下

案例1：
* ==比较的是内存地址是否相同，"abc"是存放在常量池里的，所以指向的是同一个对象，所以返回true

* equals是object里的方法，查看object源码默认==效果一样，但String类对equals进行了重写，比较内容一致就返回true，所以返回true

案例2：
* 由于a，b分别指向了两个String对象，即内存地址不一致，所以返回false
* equals和案例1原理一致，由于是String类的equals方法，所以比较的是内容


```
//Object类的equals方法
public boolean equals(Object obj) {
        return (this == obj);
    }
```
# 参考
[Java中==与equals的区别及理解](https://www.cnblogs.com/whcwkw1314/p/8044947.html)
