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

## 够将还未 commit 的代码存起来
应用场景：某一天你正在 feature 分支开发新需求，突然产品经理跑过来说线上有bug，必须马上修复。而此时你的功能开发到一半，于是你急忙想切到 master 分支,然后你就会发现，没有commit的是无法切换分支的。由于情况紧急，你只有急忙 commit 上去，commit 信息也随便写了个“暂存代码”，于是该分支提交记录就留了一条黑历史。

```
// 保存当前未commit的代码
git stash

// 保存当前未commit的代码并添加备注
git stash save "备注的内容"

// 列出stash的所有记录
git stash list

// 删除stash的所有记录
git stash clear

// 应用最近一次的stash
git stash apply

// 应用最近一次的stash，随后删除该记录
git stash pop

// 删除最近的一次stash
git stash drop
```

# 参考文件
[Git不要只会pull和push，试试这5条提高效率的命令-掘金](https://juejin.cn/post/7071780876501123085)

