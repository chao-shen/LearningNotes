# RxJava入门

## 概念
RxJava 是一个 基于事件流、实现异步操作的库
## 设计知识点概览图如下
![image](https://img-blog.csdnimg.cn/20190424231300370.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYW9fc2hlbg==,size_16,color_FFFFFF,t_70 "")

## Rxjava原理
Rxjava基于观察者模式

被观察者（Observable）	产生事件

观察者（Observer）	接收事件，并给出响应动作

订阅（Subscribe）	连接 被观察者 & 观察者	

事件（Event）	被观察者 & 观察者 沟通的载体	

RxJava原理可总结为：被观察者 （Observable） 通过 订阅（Subscribe） 按顺序发送事件 给观察者 （Observer）， 观察者（Observer） 按顺序接收事件 & 作出对应的响应动作

## 基本使用(基于事件流的链式调用)
RxJava的链式操作
```
        Observable.create(new ObservableOnSubscribe<Integer>() {
        // 1. 创建被观察者 & 生产事件
            @Override
            public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {
                emitter.onNext(1);
                emitter.onNext(2);
                emitter.onNext(3);
                emitter.onComplete();
            }
        }).subscribe(new Observer<Integer>() {
            // 2. 通过通过订阅（subscribe）连接观察者和被观察者
            // 3. 创建观察者 & 定义响应事件的行为
            @Override
            public void onSubscribe(Disposable d) {
                Log.d(TAG, "开始采用subscribe连接");
            }
            // 默认最先调用复写的 onSubscribe（）

            @Override
            public void onNext(Integer value) {
                Log.d(TAG, "对Next事件"+ value +"作出响应"  );
            }

            @Override
            public void onError(Throwable e) {
                Log.d(TAG, "对Error事件作出响应");
            }

            @Override
            public void onComplete() {
                Log.d(TAG, "对Complete事件作出响应");
            }

        });
    }
}

注：整体方法调用顺序：观察者.onSubscribe（）> 被观察者.subscribe（）> 观察者.onNext（）>观察者.onComplete()
```

输出步骤

![image](https://img-blog.csdnimg.cn/20190424232157714.png "")

取消订阅

unsubscribe()：用于取消订阅。在该方法被调用后，观察者将不再接收 & 响应事件。调用该方法前，先使用 isUnsubscribed() 判断状态，确定被观察者Observable是否还持有观察者Subscriber的引用，如果引用不能及时释放，就会出现内存泄露


[RxJava学习（全家桶）](https://www.jianshu.com/nb/14302692)