# Handler机制

主要有4个类

Handler 负责发消息和处理消息

Message 消息对象

MessageQueue 消息队列,负责存储消息对象 本质是优先队列

Looper 消息轮询器 负责从MQ中取消息并传给handler

Handler 使用 sendMessage()或者post方法发送消息,最后调用到 MessageQueue 的 enqueueMessage()方法,向队列里添加消息。这里有个关键点 Message 有个值 when(when = 当前时间戳+delay),添加消息的时候会将when的大小当做优先级将MessageQueue 重新排 列,轮训消息，这说明messagequeue取消息不是有序的

## 发送消息有两种方法
Handler.sendMessage（）、使用Handler.post（）

## Handler.sendMessage（）
首先在主线程创建handler时，构造方法里获取了主线程的looper对象，并获取了消息队列，当调用sendMessage方法时，调用handler自己的enqueueMessage方法，并将消息的target属性指向此handler，然后调用消息队列的enqueueMessage将消息添加到MessageQueue中，looper调用loop方法轮询向MessageQueue取消息，取不到，则阻塞，取到时，则调用msg.target.dispatchMessage(msg)将消息发送给handler，dispatchMessage内部最终调用handleMessage来处理looper发送过来的消息，即更新ui

## Handler.post（）
跟sendMessage方法大同小异，具体区别是post内部对Runnable进行特殊处理，通过getPostMessage(r)方法创建message对象，并将此message的callback属性设置为此Runnable，然后再和Handler.sendMessage（）方式一致.通过sendMessageDelayed方法，调用MQ的enqueueMessage，将消息添加到MessageQueue中，最后通过msg.target.dispatchMessage(msg)将消息发送给handler，dispatchMessage内部最终调用handleCallback来处理looper发送过来的消息,并最终调用Runnable的run方法，即更新ui

### 涉及的主要源码如下：

```
 private static Message getPostMessage(Runnable r) {
        Message m = Message.obtain();
        m.callback = r;
        return m;
    }
```

```
public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }

private static void handleCallback(Message message) {
        message.callback.run();
    }
```




# 参考
[Android Handler消息机制实现原理](https://www.jianshu.com/p/6cc4d4b4676b)

[Android Handler：手把手带你深入分析 Handler机制源码](https://www.jianshu.com/p/b4d745c7ff7a)
