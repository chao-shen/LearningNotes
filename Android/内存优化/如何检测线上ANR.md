# 如何检测线上ANR

## ANR监测原理
判断ANR的方法其实很简单，我们在子线程里向主线程发消息，如果过了固定时间后，消息仍未处理，则说明已发生ANR了

## 定位耗时操作的原理
当程序ANR后，我们可以通过主线程Looper拿到主线程Thread，然后通过getStackTrace拿到主线程当前的调用栈，从而定位到发生ANR的地方，定位到耗时操作。

## 代码
lastTimeTick/timeTick：用来判断消息是否被处理，初始化两个值相等并且为0，然后固定时间发送给handler，handler将timeTick执行+1操作，当过了固定时间，判断这两个字段是否相等，如果是不等的，则说明正常回调handlermessage方法；发现这两个字段相等，则说明handler回调有延迟，故产生卡顿，获取当前调用栈，并上传服务器。

代码如下：

```
		public class ANRWatchDog extends Thread {
			public static final int MESSAGE_WATCHDOG_TIME_TICK = 0;
			/**
			 * 判定Activity发生了ANR的时间，必须要小于5秒，否则等弹出ANR，可能就被用户立即杀死了。
			 */
			public static final int ACTIVITY_ANR_TIMEOUT = 2000;
 
 
			private static int lastTimeTick = 0;
			private static int timeTick = 0;
 
 
			private Handler watchDogHandler = new android.os.Handler() {
				@Override
				public void handleMessage(Message msg) {
					timeTick++;
					timeTick = timeTick % Integer.MAX_VALUE;
				}
			};
			@Override
			public void run() {
				while (true) {
					watchDogHandler.sendEmptyMessage(MESSAGE_WATCHDOG_TIME_TICK);
					try {
						Thread.sleep(ACTIVITY_ANR_TIMEOUT);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					//如果相等，说明过了ACTIVITY_ANR_TIMEOUT的时间后watchDogHandler仍没有处理消息，已经ANR了
					if (timeTick == lastTimeTick) {
						throw new ANRException();
					} else {
						lastTimeTick = timeTick;
					}
				}
			}
		}
```

# 参考
[Android中ANR的监测与定位](https://blog.csdn.net/u013771867/article/details/78484470)