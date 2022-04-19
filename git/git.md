# 常见操作

## 本地没有远端的某个分支，要切换过去
```
// 拉取远端分支到本地
git fetch

// 根据远端分支创建本地分支并切换过去
git checkout -b <your-branch-name> origin/<origin-branch-name>
```

## 本地新建分支后推送到远端

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

## 将还未 commit 的代码存起来
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



## 回滚commit到某个节点
```
// 回滚最近的一个commit
git reset HEAD^

// 回滚最近的一个commit，并保留修改内容
git reset --soft HEAD^

```
如果修改的commit已经提交过远端，添加--soft，修改以后再次push的commit会导致和远端有差异，会冲突。如果不想导致冲突，需要强制推送 git push -f 来覆盖被 reset 的 commit。
```
// 回滚到一个指定的commit，并保留修改内容
git reset --soft 1a900ac29eba73xxxxxxxxxxxbfa38f75
```
如果是回滚到指定commit，那么要注意，从当前commit到这个指定commit之间的所有commit，都会被回滚。

## 将某一个commit直接复制到另一个分支
应用场景：当有两个功能分支并行开发的时候，如果有一个修改是两个分支都要修改的，可以在一个分支开发完成并commit后，直接复制到另一个分支上。（这个地方可以感受到，合理划分commit的好处了。）

复制某个commit到另一个分支。
```
// 切换到还没有这个功能的分支上，使用以下命令，就可以复制到本分支上。
git cherry-pick commitId
```
复制多个commit到另一个分支
```
// 和复制单个差不多，多写几个commitId，以空格隔开
git cherry-pick commit1 commit2
```
复制多个连续的commit到另一个分支
```
// 和复制单个差不多，多写几个commitId，以空格隔开
git cherry-pick commit1^..commit2
```

### cherry-pick 代码冲突
复制ccommit的时候，可能会出现代码冲突的情况，当你发现分支名称带上（分支名/CHERRY-PICKING）的时候，就应该注意了。

这时需要解决代码冲突，重新提交到暂存区

然后有三种处理方式：
- 让 cherry-pick 继续进行下去
```
git cherry-pick --continue
```
- 放弃 cherry-pick
  
回到操作前的样子，就像什么都没发生过
```
git cherry-pick --abort
```
- 退出 cherry-pick

不回到操作前的样子。即保留已经 cherry-pick 成功的 commit，并退出 cherry-pick 流程。
```
git cherry-pick --quit
```

## 撤销某个commit内容
应用场景：如果线上出现了问题，但是因为已经是比较久之前提交的内容了，之间间隔了很多commit，导致无法使用reset去直接回滚。我们可能会直在线上分支拉一个hotfix分支，然后修改bug再commit，也可以使用revert，将现有的提交还原，恢复提交的内容，并生成一条还原记录。


# 参考文件
[Git不要只会pull和push，试试这5条提高效率的命令-掘金](https://juejin.cn/post/7071780876501123085)

