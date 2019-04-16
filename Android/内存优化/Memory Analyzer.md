# Memory Analyzer用法
可以先根据Memory Profiler查看内存变化，然后用Memory Analyzer分析
如下图，产生梯形说明内存不断增加，说明有内存泄漏情况
![image](https://img-blog.csdnimg.cn/20190415233659625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYW9fc2hlbg==,size_16,color_FFFFFF,t_70 "")

## 用法
用Memory Profiler的堆转储功能，生成hprof文件，该文件需要经过Android SDK提供了这个工具 hprof-conv (位于 sdk/tools下)才能被 MAT识别

命令行：hprof-conv 转换前.hprof 转换后.hprof
![image](https://img-blog.csdnimg.cn/20190415235848803.png "")


 用 MAT打开转换后的 hprof文件 
![image](https://img-blog.csdnimg.cn/20190416001003148.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYW9fc2hlbg==,size_16,color_FFFFFF,t_70 "")

