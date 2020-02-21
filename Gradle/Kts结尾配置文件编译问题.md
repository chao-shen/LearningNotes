# kts 结尾编译失败解决办法
## 原因
### Android studio识别不了该类型文件（配置文件为build.gradle.kts）

## 解决办法
### 1、在项目的根目录中添加空的build.gradle文件
### 2、将此行添加到settings.gradle.kts文件中，并gradle同步即可

```
rootProject.buildFileName = "build.gradle.kts"
```