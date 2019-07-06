# AtomicInteger原理

AtomicInteger 是一个支持原子操作的 Integer 类，它提供了原子自增方法、原子自减方法以及原子赋值方法等。其底层是通过 volatile 和 CAS 实现的，其中 volatile 保证了内存可见性，CAS 算法保证了原子性。

## volatile 变量

保证变量可见和禁止指令重排

## CAS

CAS（Compare And Swap）即比较并交换，CAS 是乐观锁技术。当多个线程尝试使用 CAS 同时更新同一个变量时，只有其中一个线程能更新变量的值，而其它线程都失败，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次尝试。

它包含三个参数：V 内存值，预期值 A，要修改的新值 B。当且仅当预期值 A 和内存值 V 相同时，将内存值 V 修改为 B，否则什么都不做

原理图如下所示：

![image](https://user-gold-cdn.xitu.io/2019/4/24/16a4e3cddb516360 "")

### CAS的缺点：

1.CPU开销较大

在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，循环往复，会给CPU带来很大的压力。(这个重新尝试的过程被称为自旋)

2.不能保证代码块的原子性

CAS机制所保证的只是一个变量的原子性操作，而不能保证整个代码块的原子性。比如需要保证3个变量共同进行原子性的更新，就不得不使用Synchronized了。

### CAS的ABA问题
比如说一个线程one从内存位置V中取出A，这时候另一个线程two也从内存中取出A，并且two进行了一些操作变成了B，然后two又将V位置的数据变成A，这时候线程one进行CAS操作发现内存中仍然是A，然后one操作成功。 
尽管线程one的CAS操作成功，但是不代表这个过程就是没有问题的。

使用AtomicStampedReference、AtomicMarkableReference解决ABA问题

### 乐观锁和悲观锁

* synchronized是悲观锁，线程一旦得到锁，其他需要锁的线程就挂起的情况就是悲观锁。

* CAS操作的就是乐观锁，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。

# 参考
[Java：CAS(乐观锁)](https://www.jianshu.com/p/ae25eb3cfb5d)

[Java并发编程—AtomicInteger 原理剖析](https://juejin.im/post/5cc00f77f265da0359487485)


