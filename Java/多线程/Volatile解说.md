# Volatile解说

## volatile原理
volatile可以保证线程可见性和有序性，但是无法保证原子性。在JVM底层volatile是采用“内存屏障”来实现的。观察加入volatile关键字和没有加入volatile关键字时所生成的汇编代码发现，加入volatile关键字时，会多出一个lock前缀指令，lock前缀指令实际上相当于一个内存屏障（也成内存栅栏）

内存屏障会提供3个功能：

    I. 它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成；且前面语句的结果对volatile变量及其后面语句可见。

    II. 会把该线程本地内存中的变量强制刷新到主内存中去，这个写会操作会导致其他线程中的缓存无效。其他线程则会从主内存中取值

    III. 如果是写操作，它会导致其他CPU中对应的缓存行为无效。

## 要使 volatile 变量提供理想的线程安全，必须同时满足下面两个条件

    I.对变量的写操作不依赖于当前值。（如number++不可以，boolean变量可以）

    II.该变量没有包含在具有其他变量的不变式中。（如果有多个volatile变量，则每个volatile变量必须独立于其他的volatile变量）

## volatile不适用的场景
volatile不适合复合操作，例如，inc++不是一个原子性操作，可以由读取、加、赋值3步组成，
```
public volatitle int inc = 0;

public void increase(){
    inc++
}
```

## 参考
[Java中volatile关键字的最全总结](https://blog.csdn.net/u012723673/article/details/80682208)
