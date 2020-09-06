# Gradle常用命令
## 构建release包
./gradlew :app:assembleMrRelease 

./gradlew clean :app:assembleMrRelease

备注：

./gradlew clean :app:assembleMrRelease --debug（可以看到日志）

## 构建release包，并安装到手机上
./gradlew :app:installMrRelease

./gradlew clean :app:installMrRelease

## 提示
Mr 可以替换成你要打的 flavor，首字母大写

比如 alpha 的话就是

 ./gradlew :app:assembleAlphaRelease