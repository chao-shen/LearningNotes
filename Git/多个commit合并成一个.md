# 多个commit合并成一个
## 合并前三个commit为1个
git rebase -i HEAD~3
![image](https://img-blog.csdnimg.cn/20201002203020749.png "")
执行如上命令后，弹出确认对话框如下
![image](https://img-blog.csdnimg.cn/20201002203247925.png "")
每一个commit id 前面的pick表示指令类型，git 为我们提供了以下几个命令:
```
pick：保留该commit（缩写:p）

reword：保留该commit，但我需要修改该commit的注释（缩写:r）

edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）

squash：将该commit和前一个commit合并（缩写:s）

fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）

exec：执行shell命令（缩写:x）

drop：我要丢弃该commit（缩写:d）
```
根据我们的需求，我们将commit内容编辑如下:
```
pick 4d73de9 第一次提交
s 724656d 第二次提交
s 58d3249 第三次提交
```
上面的意思就是把第二次、第三次提交都合并到第一次提交上

然后:wq保存退出后是注释修改界面:
![image](https://img-blog.csdnimg.cn/20201002203615531.png "")

最终的编辑效果如下(在浏览态，按下两个dd可以删除一行)：
![image](https://img-blog.csdnimg.cn/20201002203838785.png "")

按esc退出编辑模式，并且输入:wq退出对话框,发现已经合并为一个commit了

## 注意
多个commit最好是本地的，而不是push到远端的，因为多人合作时，git规范，本地解决冲突，一般push前都会rebase，在rebase后，合并的commit会被还原。（可以考虑强制push，但注意代码覆盖问题）
