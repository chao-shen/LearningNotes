# 回滚push到本地
## 第一步 还原本地记录
```
git reset --mixed <版本号>
// 注意使用 --hard 参数会抛弃当前工作区的修改
// 使用 --soft 参数的话会回退到之前的版本，但是保留当前工作区的修改，可以重新提交
// 使用 --mixed 参数的话会回退到之前的版本，之前所有提交的文件还原到本地且脱离git管理
```

## 第二步 假如想push若干文件到远程（10个，push其中五个）
### 可以把要提交的文件add后，然后commit到本地

## 第三步 push命令提交到远程
```
git push origin <分支名>
```

### 注意：会提示本地的版本落后于远端的版本 
![image](https://img-blog.csdnimg.cn/20200305132251322.png "")

### 为了覆盖掉远端的版本信息，使远端的仓库也回退到相应的版本，需要加上参数--force(-f)
```
git push origin <分支名> --force
```

需要注意：force强制push可能会覆盖别人的代码(同一分支协同开发)，所以建议用如下命令
```
git push origin <分支名> --force-with-lease
```
如果出现冲突，仍会给出和没有force一样的拒绝提示，先fetch再rebase即可

### 这样就可以完成reset远程文件到本地且提交若干个文件了
