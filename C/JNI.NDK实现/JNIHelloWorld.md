# JNIHelloWorld

## 交叉编译
* 在一个平台下编译出另一个平台下可以运行的本地代码
*** cpu平台  x86 arm(armeabi) mips**
* 操作系统平台 windows linux  mac os unix
* 原理 在一个平台下模拟另一个平台的特点去编译本地代码

* NDK native develop kit  SDK
* NDK 目录结构
	* docs 帮助文档
	* platforms 根据不同的android版本号分文件夹
		* include 头文件
		* lib  谷歌提供的做jni开发可以用到的库
	* samples 谷歌提供的样例工程 供参考
	* sourcese jni相关源代码
	* build->tools .sh文件 linux平台下的批处理文件 交叉编译时会自动调用
	* ndk-build.cmd
* CDT 高亮C的关键字  代码提示

## JNIHelloWorld（Java打印C中的HelloWorld）
* jni开发流程
* java native interface
	* ①写java代码 声明本地方法 用关键字 native 本地方法不用实现 具体的实现在C代码中
	* ② 创建jni目录 在主model的根目录下创建
	* ③ 创建Android.mk  linux下的makefile文件
		* 向编译系统描述一下 我要编译的代码在哪儿 生成一个叫什么名字的文件
		
				* LOCAL_PATH := $(call my-dir)
		
		    	include $(CLEAR_VARS)
		
		    	LOCAL_MODULE    := hello   #System.loadlibrary("hello")
		    	LOCAL_SRC_FILES := hello.c #.c源文件的名字

    			include $(BUILD_SHARED_LIBRARY)
	* ④ 创建C源码 本地函数名的命名规则 Java_包名_类名_本地方法名
	
	可以在主model的具体路径，比如我的是cd Chaoshen\src\main\java。然后通过javah命令生成该类的头文件.这里是javah -jni com.chao.chaoshen.jni.JniUtils，生成头文件com_chao_chaoshen_jni_JniUtils.h 
	命令如下
	![image](https://img-blog.csdnimg.cn/20190528234228648.png "")

	在jin路径创建C文件并将生成头文件的方法拷贝到进去 记得必须导入 jni.h头文件

![image](https://img-blog.csdnimg.cn/20190528232604912.png "")
![image](https://img-blog.csdnimg.cn/20190528232726645.png "")
		
	* ⑤ 调用ndk-build编译源代码  到主model根目录下运行ndk-build命令就会在项目中自动生成so包
	![image](https://img-blog.csdnimg.cn/20190528232812340.png "")
    ![image](https://img-blog.csdnimg.cn/2019052823290835.png "")
	* ⑥ java代码中 调用System.loadlibrary("")来加载so包

## 实例效果(点击按钮)
![image](https://img-blog.csdnimg.cn/2019052823340078.png "")

## jni开发常见错误

	* 本地方法没有找到  java.lang.UnsatisfiedLinkError: Native method not found: 
		* 本地方法名写错
			* 避免写错 javah生成头文件
			* jdk1.7以上 到项目/src目录下去运行 生成的头文件就在src目录下
			* jdk1.6 到项目/bin/classes 目录下运行javah 生成的头文件在  /bin/classes去找
			* javah +声明本地方法的类的全类名
		* 忘记加载动态链接库 没有调用
			* System.loadLibrary("hello");
	* 找动态链接库返回空 java.lang.UnsatisfiedLinkError: ..... findLibrary returned null
		* System.loadLibrary("hello"); 动态链接库的名字写错
		* 没有编译出对应平台的.so文件 需要手动创建 Application.mk 指定
			* APP_ABI := armeabi x86 
			* APP_PLATFORM := android-14  解决编译时警告的问题

# 注意

## C中JNIEnv *env, jobject jclass参数含义

### *env

结构体JNINativeInterface定义了大量的函数指针,JNIEnv是结构体JNINativeInterface的一级指针,env 是JNIEnv 的一级指针也就是JNINativeInterface的二级指针

(**env).函数指针  (*env)->函数指针

### jobject jclass
jobject jclass 哪个类调用的本地函数 这个jclass 就是这个类的对象（在这个例子中 jclass 指的就是JniUtils的对象）

## 想要代码有提示功能，并且可以关联到相关的头文件

在主model里添加
```
externalNativeBuild {
        ndkBuild {
            path 'jni/Android.mk'
        }
    }
```
