# app启动流程
![image](https://img-blog.csdnimg.cn/20190423230238475.jpg "")

App启动时，AMS会检查这个应用程序所需要的进程是否存在，不存在就会请求Zygote进程启动需要的应用程序进程，Zygote进程接收到AMS请求并通过fock自身创建应用程序进程，这样应用程序进程就会获取虚拟机的实例，还会创建Binder线程池（ProcessState.startThreadPool()）和消息循环(ActivityThread looper.loop),然后App进程，通过Binder IPC向sytem_server进程发起attachApplication请求；system_server进程在收到请求后，进行一系列准备工作后，再通过Binder IPC向App进程发送scheduleLaunchActivity请求；App进程的binder线程（ApplicationThread）在收到请求后，通过handler向主线程发送LAUNCH_ACTIVITY消息；主线程在收到Message后，通过反射机制创建目标Activity，并回调Activity.onCreate()等方法。到此，App便正式启动，开始进入Activity生命周期，执行完onCreate/onStart/onResume方法，UI渲染结束后便可以看到App的主界面。

[【凯子哥带你学Framework】Activity启动过程全解析](https://www.jianshu.com/p/6037f6fda285)

