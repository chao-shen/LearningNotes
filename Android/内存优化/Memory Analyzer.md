# Memory Analyzer用法
可以先根据Memory Profiler查看内存变化，然后用Memory Analyzer分析
如下图，产生梯形说明内存不断增加，说明有内存泄漏情况

![image](https://img-blog.csdnimg.cn/20190415233659625.png "")

## 用法
用Memory Profiler的堆转储功能，生成hprof文件，该文件需要经过Android SDK提供了这个工具 hprof-conv (位于 sdk/tools下)转换才能被 MAT识别

### 命令行：hprof-conv 转换前.hprof 转换后.hprof
![image](https://img-blog.csdnimg.cn/20190415235848803.png "")


 ### 用 MAT打开转换后的 hprof文件 
![image](https://img-blog.csdnimg.cn/20190416001003148.png "")

### histogram（直方图）介绍
直方图显示内存占有情况，可以过滤类名或者按照包名查找
![image](https://img-blog.csdnimg.cn/20190417205821803.png "")

搜索HeapLeakActivity然后选择incoming Reference，代表改类对象被谁引用了
![image](https://img-blog.csdnimg.cn/20190417210156670.png "")

![image](https://img-blog.csdnimg.cn/20190417210609135.png "")

![image](https://img-blog.csdnimg.cn/20190417210815982.png "")

### dominator tree（支配树）介绍
该功能主要展示对象占用总消耗内存的百分比，占用内存较大的需要处理优化
![image](https://img-blog.csdnimg.cn/20190417211135165.png "")