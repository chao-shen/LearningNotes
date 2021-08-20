# 撤销中间某次commit

```
git revert commit_id
//如果commit_id是merge节点的话,-m是指定具体哪个提交点
git revert commit_id -m 1
//接着就是解决冲突
git add -A
git commit -m ".."
git revert commit_id -m 2
//接着就是解决冲突
git add -A
git commit -m ".."
git push
```

其中git revert commit_id -m 数字是针对，merge提交点的操作。

解决冲突，后续步骤和rebase一致
```
 (all conflicts fixed: run "git revert --continue")
  (use "git revert --skip" to skip this patch)
  (use "git revert --abort" to cancel the revert operation)

```