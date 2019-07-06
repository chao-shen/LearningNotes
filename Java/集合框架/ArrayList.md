# ArrayList底层

基于数组实现，默认大小为10，超过10则进行扩容处理，扩容为原数组的长度的1.5倍，grow是核心扩容方法

##  ArrayList 没有自动缩容机制
但ArrayList提供了缩容方法，trimToSize

# 参考

[原来 ArrayList 内部原理这么简单](https://juejin.im/post/5c8252cee51d453a5f22b391)

[ArrayList底层数组扩容原理 - Java那些事儿专栏](https://juejin.im/post/59dd70966fb9a0452845741e)
