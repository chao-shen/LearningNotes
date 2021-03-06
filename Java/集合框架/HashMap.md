# HashMap原理

## 实现
数组+链表

散列表（HashMap）中维护了一个数组，数组的每一个元素被称为一个桶（bucket），散列表（HashMap）会先把key传入散列（hash）函数中进行寻址，得到的结果就是数组的下标，然后再通过这个下标访问数组即可得到相关联的值

![image](https://img-blog.csdnimg.cn/20190421224635798.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYW9fc2hlbg==,size_16,color_FFFFFF,t_70 "")

## 碰撞冲突
hash算法具有唯一性，但同时它还具有重复性，唯一性保证了相同输入的输出是一致的，却没有保证不同输入的输出是不一致的，也就是说，完全有可能两个不同的key被分配到了同一个bucket（因为它们的hash code可能是相同的）

HashMap的散列函数具体流程如下图：

(异或，相同为0，不同为1

 与，都为1则为1，否则为0)

![image](https://img-blog.csdnimg.cn/20190421230126417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYW9fc2hlbg==,size_16,color_FFFFFF,t_70 "")

## 解决冲突
### 拉链法
主要思想是每个bucket都应当是一个互相独立的数据结构，当发生冲突时，只需要把数据放入bucket中（因为bucket本身也是一个可以存放数据的数据结构），这样查询一个key所消耗的时间为访问bucket所消耗的时间加上在bucket中查找的时间。

#### 具体思路
HashMap的buckets数组其实就是一个链表数组，在发生冲突时只需要把Entry放到链表的尾部，如果未发生冲突（位于该下标的bucket为null），那么就把该Entry做为链表的头部。

![image](https://img-blog.csdnimg.cn/20190421231841839.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYW9fc2hlbg==,size_16,color_FFFFFF,t_70 "")

#### 解决冲突优化(jdk1.8)
大部分的操作都是在进行查找，碰撞冲突可能会频繁发生，链表也会变得越来越长，这会导致查询变得低效(效率O(n))，Java 8对其实现了优化，链表的节点数量在到达阈值时会转化为红黑树，这样查找所需的时间就只有O(log n)了

特点：插入Entry时发现一条链表超过阈值，会对该链表进行树化；如果在删除Entry（或进行扩容）时发现红黑树的节点太少也会把红黑树退化成链表。

#### put过程（JDK1.8版）
1.对Key求Hash值，然后再计算下标

2.如果没有碰撞，直接放入桶中（碰撞的意思是计算得到的Hash值相同，需要放到同一个bucket中）

3.如果碰撞了，则调用equals()比较key，key相同则替换旧值，不同则以链表的方式链接到后面

4.如果链表长度超过阀值( TREEIFY THRESHOLD==8)，就把链表转成红黑树，链表长度低于6，就把红黑树转回链表

5.如果桶满了(容量16*加载因子0.75)，就需要 resize（扩容2倍后重排）

#### get过程
当我们调用get()方法，HashMap会使用key的hashcode找到bucket位置，找到bucket位置之后，会调用keys.equals()方法去找到链表中正确的节点，最终找到要找的value对象。

### 开放寻址法
在开放寻址法中，所有Entry都会存储在buckets数组，一个明显的区别是，拉链法中的每个bucket都是一个链表或其他的数据结构，而开放寻址法中的每个bucket就仅仅只是Entry本身

#### 具体思路
开放寻址法有多种不同的实现，我们介绍一种最简单的算法：线性探测法（Linear probing），在发生碰撞时，简单地将索引加一，如果到达了数组的尾部就折回到数组的头部，直到找到目标或一个空位。

![image](https://img-blog.csdnimg.cn/20190421233221776.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYW9fc2hlbg==,size_16,color_FFFFFF,t_70 "")

## 动态扩容
用一个容量更大（在原容量上乘以二）的数组来替换掉当前的数组，这个过程需要把旧数组中的数据重新hash到新数组，所以扩容也能在一定程度上减缓碰撞。

HashMap通过负载因子（Load Factor）乘以buckets数组的长度来计算出临界值，算法：threshold = load_factor * capacity。比如，HashMap的默认初始容量为16（capacity = 16），默认负载因子为0.75（load_factor = 0.75），那么临界值就为threshold = 0.75 * 16 = 12，只要Entry的数量大于12，就会触发扩容操作。

注意：HashMap不会动态地进行缩容，一个已经删除过大量Entry的HashMap（如果不打算继续添加元素的话），此时它的buckets数组经过多次扩容已经变得非常大了，这会占用非常多的无用内存，建议，丢掉这个旧HashMap，并将数据转移到一个新的HashMap。

## 添加元素
HashMap内部维护了一个数组，每一个key都会经过散列函数得出在数组的索引，如果两个key的索引相同，那么就使用拉链法解决碰撞冲突，当Entry的数量大于临界值时，对数组进行扩容。

## HashMap如何保证元素均匀分布
hash & (length-1)

index值是：通过Key值的hashCode值和hashMap长度-1做与运算

hashmap中的元素，默认情况下，数组大小为16，也就是2的4次方，如果要自定义HashMap初始化数组长度，也要设置为2的n次方大小，因为这样效率最高。因为当数组长度为2的n次幂的时候，不同的key算出的index相同的几率较小，那么数据在数组上分布就比较均匀，也就是说碰撞的几率小，相对的，查询的时候就不用遍历某个位置上的链表，这样查询效率也就较高了

## 参考
[Map大家族的那点事儿(4) ：HashMap](http://www.importnew.com/29724.html)

[HashMap？我是谁？我在哪](https://www.cnblogs.com/zhuoqingsen/p/HashMap.html)

