# 提高SharedPreferences写入效率

1、apply没有返回值而commit返回boolean表明修改是否提交成功

2、apply是将修改数据原子提交到内存, 而后异步真正提交到硬件磁盘, 而commit是同步的提交到硬件磁盘

3、apply方法不会提示任何失败的提示

apply的效率高一些，如果没有必要确认是否提交成功建议使用apply。