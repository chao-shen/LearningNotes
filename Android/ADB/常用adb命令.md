# 常用adb命令
## adb命令安装apk
1.在apk根目录打开终端

2.首次安装：adb install xxx/xxx/xxx（apk路径）【拖动apk至终端即可获取apk路径】

3.覆盖安装：adb install -r xxx/xxx/xxx（apk路径）

备注：
apk安装，不会自动打开apk，需手动打开

编译release包，debuggable 改成true，minifyEnabled 改成 false ，屏蔽压缩、混淆等时间
```
debuggable true
minifyEnabled false
```