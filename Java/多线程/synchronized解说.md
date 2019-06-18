# synchronized

Java中每一个对象都可以作为锁，这是synchronized实现同步的基础：

* 普通同步方法，锁是当前实例对象
* 静态同步方法，锁是当前类的class对象
* 同步方法块，锁是括号里面的对象

## synchronized底层原理
### 同步代码块
当Java源代码被javac编译成bytecode的时候，会在同步块的入口位置和退出位置分别插入monitorenter和monitorexit字节码指令。JVM需要保证每一个monitorenter都有一个monitorexit与之相对应。任何对象都有一个monitor与之相关联，当且一个monitor被持有之后，他将处于锁定状态。线程执行到monitorenter指令时，将会尝试获取对象所对应的monitor所有权，即尝试获取对象的锁

### 同步方法
在Class文件的方法表中将该方法的access_flags字段中的synchronized标志位置1，表示该方法是同步方法并使用调用该方法的对象或该方法所属的Class在JVM的内部对象表示Klass做为锁对象

### 为什么说synchronized是重量级锁
synchronized会使争用不到锁的线程进入阻塞状态，而阻塞或唤醒一个线程需要操作系统在用户态和核心态之间切换，会消耗大量的资源，所以所synchronized是一把重量锁。

(JDK1.6之后，对synchronized做了大量优化。)

# 参考
[死磕Java并发：深入分析synchronized的实现原理](http://www.importnew.com/23511.html)
