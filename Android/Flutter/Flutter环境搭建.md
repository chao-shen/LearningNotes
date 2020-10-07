# Flutter环境搭建
## 下载Flutter SDK(官方建议下载beta分支)
```
git clone -b beta https://github.com/flutter/flutter.git
```
## 配置"环境变量"
### 终端中打开bash_profile文件 进行配置（这个文件用Mac开发过的应该都会有，如果没有，自己创建bash_profile)
```
open ~/.bash_profile
```
### 在最后面插入下列代码
```
export PATH=你文件所在目录的路径/flutter/bin:$PATH
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
export PUB_HOSTED_URL=https://pub.flutter-io.cn
```

### 接着保存退出后，在终端输入 source ~/.bash_profile 即可使用flutter
```
source ~/.bash_profile
```

### AS配置Dart和Flutter插件
![image](https://img-blog.csdnimg.cn/20201007205905134.png "")

### 下面代码用于检测你还有哪些部分没有配置完成
```
flutter doctor
```
![image](https://img-blog.csdnimg.cn/20201007205619937.png "")

### flutter doctor命令检测，提示配置都已经完成，就说明可以创建flutter项目并使用了


# 参考
[Flutter热重载](https://flutterchina.club/get-started/test-drive/#androidsstudio)