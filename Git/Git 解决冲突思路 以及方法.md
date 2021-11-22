# Git 解决冲突思路 以及方法

[TOC]

在代码版本控制中, git 工具占据了半壁江山, 是项目开发中不可或缺的工具之一

## 前提

作为程序员, 保持优秀的代码迭代是良好的传统, 但是在实际项目开发中, 由于多人项目合作完成一个项目, 难免在提交合并过程中出现代码冲突, 然而如何优雅的解决这些冲突, 就是我们另外一个要掌握的技能了

## 经历

作为程序员的我, 多长一段时间内, 都不会真的使用代码冲突解决的思路,  一直使用在本地分支 直接 使用`git pull origin master` 作为合并或者保持本地分支获取最新的 master 代码, 但是终于有一天被坑大了, 一个项目上线后, 在我拉去代码由于冲突太多, 差点把手上的完成的项目搞乱, 重头开发(主要是担心辛辛苦苦开发代码没有了); 终于下定决心整理如何优雅的解决冲突

## 操作

```shell
# 切换到 master
git checkout master
# 更新最新代码
git pull origin master
# 切回分支
git checkout local
# rebase, 主要: 保证 loca 分支中的新增和改变都 add 和 commit, 否则 rebase 将会失败
git rebase master
# 如果有冲突, 则进入另外一个临时分支, 在此分支下解决冲突

####################################
# 以上可简写::                      #
# git pull origin master --rebase #
####################################

git add filename # 解决冲突文件
# 将所有文件冲突解决完成后
git rebase --continue # 即可回到 local 分支
```



```shell

```

先把 vendor 删除 并 commit

```shell
cd order
# 拉取当前分支最新代码
git pull 
# 删除 vendor 并提交
rm -rf vendor  
git add -A
git commit -m'delete vendor'

# 合并分支, 当前以合并 master 分支到当前分支为例
git pull origin master
# 解决冲突
# 解决思路:
#   1. 解决 Gopkg.lock, 以便之后的 dep
#   2. 解决 非 vendor 目录下的文件冲突
#   3. 忽略 vendor 目录下的冲突
#   4. 全部完成则 继续 下方操作
git add .
git commit -m'fix confle ct'

# 重新下载依赖
dep ensure -update -v
# 将最新依赖加入版本
git add .
git commit -m'dep vendor'

# # 缺点: graph 会非常难看
```

