# Gradle常用命令
## 构建release包
./gradlew :app:assembleMrRelease 

./gradlew clean :app:assembleMrRelease

备注：

./gradlew clean :app:assembleMrRelease --debug（可以看到日志）

./gradlew assembleDebug --stacktrace -info (编译看日志)

./gradlew assembleDebug --stacktrace -info > log.txt  (编译看日志，输出到log文件中)

## 构建release包，并安装到手机上
./gradlew :app:installMrRelease

./gradlew clean :app:installMrRelease

## 提示
Mr 可以替换成你要打的 flavor，首字母大写

比如 alpha 的话就是

 ./gradlew :app:assembleAlphaRelease

 ## 输出组件依赖
```
 ./gradlew clean :app:dependencies >| ~/Documents/$(basename `pwd`).dependencies.txt
```

### release包 ,取消混淆+可调试
在主项目中app目录的build.gradle中加入如下配置

```
 debuggable true
 minifyEnabled false
```
完整如下：

```
buildTypes {
        release {
            debuggable true
            minifyEnabled false
            // 如果执行的是 assembleMrRelease，那么跳过 shrinkResources
            shrinkResources minifyEnabled && !isMrRelease && !disableShrinkRes
            proguardFiles getDefaultProguardFile('proguard-android.txt')
            proguardFiles file("proguard-rules").listFiles()
        }
    }
```

