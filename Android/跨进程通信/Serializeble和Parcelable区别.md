# Serializeble和Parcelable区别

Serializeble，来自java中序列化的接口，主要注意的是Serializeble在序列化时会产生大量的临时变量，从而引起频繁的垃圾回收，频繁的垃圾回收会影响ui性能，造成ui卡顿，内存抖动，最后可能造成oom 

parcelable，来自Android中自带的序列化方式，在使用内存时parcelable比Serializeble性能更好，但parcelable有个明显的缺点，就是不能使用在要将数据存储在磁盘上的情况，它的本质是更好的实现对象在进程间通信的传递，它并不是一个通用的序列化机制，它用的场合基本上是在进程间的通信，保守的话还是采用Serializeble序列化会比较安全
