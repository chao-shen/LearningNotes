# Binder机制
## 为什么Android使用Binder通信
### 性能方面
Binder相对出传统的Socket方式，更加高效。Binder数据拷贝只需要一次，而管道、消息队列、Socket都需要2次

### 安全方面
传统的进程通信方式对于通信双方的身份并没有做出严格的验证，比如Socket通信ip地址是客户端手动填入，很容易进行伪造，而Binder机制从协议本身就支持对通信双方做身份校检，因而大大提升了安全性。

### 稳定性
Binder是基于C/S架构的，Client端有什么需求，直接发送给Server端去完成，架构清晰明朗，Server端与Client端相对独立，稳定性较好

## Binder原理

Client进程：使用服务的进程。

Server进程：提供服务的进程。

ServiceManager进程：ServiceManager的作用是将字符形式的Binder名字转化成Client中对该Binder的引用，使得Client能够通过Binder名字获得对Server中Binder实体的引用。

Binder驱动：驱动负责进程之间Binder通信的建立，Binder在进程之间的传递，Binder引用计数管理，数据包在进程之间的传递和交互等一系列底层支持。

## 执行过程

1.Server进程会先注册Service到ServiceManager，告诉ServiceManager中server里的方法信息

2.client通过servicemanager获得一个server的service代理接口，对server进行调用。

3.client调用代理接口中的方法时，代理接口的方法会将client传递的参数打包成Parcel对象。

4.代理接口将Parcel对象发送给内核binder驱动。

5.server会读取内核binder驱动中的请求数据，如果是发送给自己的，解包Parcel对象，处理并将结果返回。

##  IPC通信有哪些

1.bundle ：

简单易用  但是只能传输Bundle支持的对象 常用于四大组件间进程间通信

2.文件共享：

简单易用  但不适合在高并发的情况下 并且读取文件需要时间 不能即时通信   常用于并发程度不高 并且实时性要求不高的情况

3.AIDL ：

功能强大 支持一对多并发通信 支持即时通信   但是使用起来比其他的复杂 需要处理好多线程的同步问题  常用于一对多通信 且有RPC 需求的场合(服务端和客户端通信)

4.Messenger ：

功能一般 支持一对多串行通信 支持实时通信  但是不能很好处理高并发情况 只能传输Bundle支持的类型  常用于低并发的无RPC需求一对多的场合

5.ContentProvider ：

在数据源访问方面功能强大 支持一对多并发操作 可扩展call方法  可以理解为约束版的AIDL  提供CRUD操作和自定义函数  常用于一对多的数据共享场合

6.Socket ：

功能强大 可以通过网络传输字节流 支持一对多并发操作  但是实现起来比较麻烦 不支持直接的RPC   常用于网络数据交换

![image](https://upload-images.jianshu.io/upload_images/3985563-5ff2c4816543c433.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/921/format/webp "")

# 参考
[简单理解Binder机制的原理](https://www.jianshu.com/p/4920c7781afe?from=jiantop.com)
