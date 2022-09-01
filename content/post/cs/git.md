---
title: "Git"
date: 2022-01-28T16:31:52+08:00
tags: ["Git"]
categories: ["original"]
draft: false
---

## 配置用户名和邮箱

```Bash
git config --global user.name "this is my username"
git config --global user.email "email@example.com"
# --global代表本机所有仓库都使用这个配置，不带的话指当前仓库
# 这个配置与账号密码没有关系，只是在项目里面的标识
# 当前仓库下的配置在./git/config
# 全局的配置在~/.gitconfig文件里
```

## 查看当前git配置

```Bash
git config --list
```

## 撤销修改

```Bash
git checkout -- readme.txt  # 这个--必须加
# 1. 修改后还没有添加到暂存区，这种情况下就会回到和版本库里面一样的状态
# 2. 如果已经添加到暂存区，还修改了，那就回到添加到暂存区后的状态
# checkout是用版本库里面的版本替换工作区的版本，不管是修改还是删除，都可以用

git reset HEAD readme.txt
# 当文件已经被放在暂存区，但还没有提交时，使用这个命令可以把暂存区内的修改撤销掉，重新放入工作区
# 用于取消已缓存的内容
```

## 生成秘钥私钥

```Bash
ssh-keygen -t rsa -C "email@example.com" 
```

## 查看当前的远程库

```Bash
git remote
git remote -v  # -v可以查看到每个别名的实际仓库地址
```

## 本地仓库关联远程仓库

```Bash
git remote add origin https://github.com/BingYm/qczll.git  # 后面这是远程仓库地址
```

## 删除远程仓库

```Bash
git remote rm origin2  # origin2为别名，可以用git remote查看
```

## 生成秘钥私钥

```Bash
ssh-keygen -t rsa -C "email@example.com" 
# 一路回车，到用户家目录下会有一个.ssh目录，里面有id_rsa和id_rsa.pub两个文件
```

## 本地仓库关联远程仓库

```Bash
git remote add origin https://github.com/BingYm/qczll.git  # 后面这是远程仓库地址
```

## 删除远程仓库

```Bash
git remote rm origin2  # origin2为别名，可以用git remote查看
```

## 增加/删除文件

1. `git add [file1] [file2]`添加指定文件到暂存区
2. `git add [dir]`添加指定目录到暂存区，包括子目录
3. `git add .`添加当前目录的所有文件到暂存区
4. `git add -A` 添加所有修改和新增的文件到暂存区
5. `git add -p`添加每个变化前都要求确认
6. `rm filename`删除，之后需要使用`git add filename`添加到暂存区，然后再commit提交，完成删除操作
7. `git rm [file1] [file2]`删除工作区文件，并将这次删除放入暂存区，如果删除之前修改过并且已经放在暂存区，则需要使用强制删除选项`-f`
8. `git rm --cached [file]`停止追踪指定文件，但该文件会保留在工作区(现在已经有文件被add到暂存区了，使用这个命令可以停止追踪该文件，重新变成未add的状态)
9. `git mv [file-original] [file-renamed]`改名，并放入暂存区

## 代码提交

1. `git commit -m [message]`提交暂存区到仓库区
2. `git commit [file1] [file2] ...-m [message]`提交暂存区的指定文件到仓库区
3. `git commit -a`提交工作区自上次提交之后的变化到仓库区
4. `git commit -v`提交时显示所有diff信息

## 分支

1. `git branch [-v]`列出所有本地分支
2. `git branch -r`列出所有远程分支
3. `git branch -a`列出所有本地分支和远程分支
4. `git branch [branch-name]`新建一个分支，但依然停留在当前分支
5. `git checkout -b [branch]`新建一个分支并切换到该分支
6. `git branch [branch] [commit]`新建一个分支，指向指定commit
7. `git branch --track [branch] [remote-branch]`新建一个分支，与指定的远程分支建立追踪关系
8. `git checkout [branch-name]`切换到指定分支，并更新工作区
9. `git checkout -`切换到上一个分支
10. `git branch --set-upstream [branch] [remote-branch]`建立追踪关系，在现有分支与指定的远程分支之间
11. `git merge [branch]`合并指定分支到当前分支
12. `git cherry-pick [commit]`选择一个commit，合并进当前分支
13. `git branch -d [branch-name]`删除分支
14. `git push origin --delete <branchName>` 删除远程分支
15. `git push origin --delete [branch-name] / git branch -dr [remote/branch]`删除远程分支

### 合并分支

1. 切换到接受修改的分支(被合并，增加新内容)上，`git checkout [被合并的分支名]`
2. 执行merge命令`git merge [有新内容的分支名]`

## 标签

1. `git tag`
2. `git tag [tag]`
3. `git tag [tag] [commit]`
4. `git tag -d [tag]`
5. `git push origin :refs/tags/[tagName]`
6. `git show [tag]`
7. `git push [remote] [tag]`
8. `git push [remote] --tags`
9. `git checkout -b [branch] [tag]`

## 查看信息

1. `git status`显示有变更的文件(工作区和暂存区)
2. `git log`显示当前分支的版本历史
3. `git log --stat`显示commit历史，以及每次commit发生变更的文件
4. `git log -S [keyword]`根据关键词搜索提交历史
5. `git log --follow [file] / git whatchanged [file]`查看某个文件的版本历史，包括文件名
6. `git log -p [file]`显示指定文件的每一次diff
7. `git log --pretty=oneline`美化行内显示每个log
8. `git log --oneline` 行内显示log，与上面的区别是hash值显示7位
9. `git log -5 --pretty --oneline`显示过去5次提交，一行一个
10. `git blame [file]`显示指定文件是什么人在什么时候修改过
11. `git diff`显示暂存区和工作区的区别
12. `git diff --cached [file]`显示暂存区和上一次commit的差异
13. `git diff HEAD`显示工作区和当前分支最新commit的差异
14. `git diff [first-branch]...[second-branch]`显示两次提交之前的差异
15. `git reflog`显示当前分支的最近几次提交，显示移到到某次提交需要的次数(HEAD@{步数})

## 远程同步

1. `git fetch [remote]`下载远程仓库的所有变动
2. `git remote -v`显示所有远程仓库(看到每个别名的实际仓库地址)
3. `git remote`显示当前的远程仓库
4. `git remote show [remote]`显示某个远程仓库的信息
5. `git remote add [shortname] [url]`增加一个新的远程仓库，并命名
6. `git pull [remote] [branch]`取回远程仓库的变化，并与本地分支合并
7. `git push [remote] [branch]`上传本地指定分支到远程仓库
8. `git push [remote] --force`强制推送当前分支到远程仓库，即使有冲突
9. `git push [remote] --all`推送所有分支到远程仓库

## 撤销

1. `git checkout [file]`恢复存区的指定文件到工作区
2. `git check [commit] [file]`恢复某个commit的指定文件到暂存区和工作区
3. `git checkout`恢复暂存区的所有文件到工作区
4. `git reset [file]`重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
5. `git reset --hard`重置暂存区与工作区，与上次commit一致
6. `git reset --hard 索引值`指针移到到索引所在位置
7. `git reset --hard HEAD^`后退一步
8. `git reset --hard HEAD~3`后退三步
9. `git reset`参数

- `--soft`仅仅在本地库移动HEAD指针

- `--hard`在本地库移动HEAD指针，重置暂存区

- `--mixed`在本地库移动HEAD指针，重置暂存区，重置工作区

1. `git reset --keep [commit]`重置当前HEAD为指定commit，但保持暂存区和工作区不变
2. `git revert [commit]`新建一个commit，用来撤销指定commit，后者的所有变化都将被前者抵消，并且应用到当前分支

## submodule

```bash
git submodule update --init --recursive
# https://stackoverflow.com/questions/1030169/pull-latest-changes-for-all-git-submodules
```

## 生成一个可供发布的压缩包

```Bash
git archive
```

## 案例

- 修改commit信息 `git commit --amend`

- 远程分支强制覆盖本地

```Bash
git fetch --all  # 取回远程库的所有修改
git reset --hard origin/master  # 指向远程库origin的master
```

- 解决每次需要pull/push操作前需要输入密码的问题

```Bash
git config --global credential.helper store
#　原因：git clone的时候使用的是https地址而非git地址
```

- 跨分支查找commit

```shell
git grep "demo" $(git rev-list --all)
```

