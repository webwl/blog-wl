# 常见操作

## 本地没有远端的某个分支，要切换过去
```
// 拉取远端分支到本地
git fetch

// 根据远端分支创建本地分支并切换过去
git checkout -b <your-branch-name> origin/<origin-branch-name>
```

## 本地新建分支后，如何推送到远端

远程已有remote_branch分支但未关联本地分支local_branch且本地已经切换到local_branch
```
// 加上了-u参数，Git不但会把本地的分支内容推送的远程新的分支，还会把本地的分支和远程的分支关联起来，在以后的推送或者拉取时就可以简化命令。
git push -u origin/<branch-name>
```

远程没有remote_branch分支并，本地已经切换到local_branch
```
git push origin local_branch:remote_branch

git push --set-upstream origin remote_branch
```


