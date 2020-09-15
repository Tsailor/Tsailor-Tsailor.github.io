---
title: Git分支管理
date: 2020-07-23
tags: 
- tools
- git
---
1、git创建本地分支：`git branch [name]` 
2、git删除本地分支：`git branch -d [name]`
3、git切换分支：`git checkout [name]`
4、git创建并切换到新的本地分支：`git checkout -b [name]`
5、git查看所有分支信息：`git branch -a` 
<!--more-->
```
*master   :本地分支， * 当前所在分支
 hexo     ：本地hexo分支
 remotes/origin/hexo   ： 远程分支
```
6、git创建远程分支：`git push origin [name]` (将本地新建的分支推送到远程)
7、git删除远程分支：`git push origin --delete [name]`
8、本地分支与远程分支建立关联：`git branch --set-upstream-to remotes/origin/hexo`

实例：将本地hexo分支的内容推送到远程hexo分支
1、新建并切换到本地hexo分支 ：`git branch hexo` 
2、将本地分支推送到远程分支：`git push origin hexo`
3、本地分支与远程分支关联： `git branch --set-upstream-to remotes/origin/hexo`
4、本地代码提交到版本库：`git add . git commit -m "message"`
5、拉取远程分支内容：`git pull origin hexo`
6、本地代码提交到远程分支：`git push origin hexo`  
