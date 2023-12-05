---
title: 命令
date: 2023-12-01
tags: 命令
description: 各种常用命令
---
# git命令

## branch
* 查看本地分支列表，也可一起查看远程分支列表
`git branch`
`git branch -a`
* 创建一个分支，以当前所在分支为父节点
`git branch 新分支名`
* 删除本地分支，需先切换到其他分支，强制删除 -D
`git branch -d 分支名`
* 删除远程分支（慎用）
`git push origin --delete 分支名`

## checkout
* 切换分支
`git checkout 分支名`
* 创建一个分支并切换过去
`git checkout -b 新分支名`
* 在本地创建一个分支，并且以线上分支作为父节点，并建立了追踪关系
`git checkout -b 新分支名 origin/线上分支名`

*克隆代码时也可指定分支并建立联系*
`git clone -b 被克隆的分支名 仓库地址`

## push
* 将本地代码推送到远程分支（当前分支只有一个远程分支或者已经和远程分支建立了联系）
`git push`
* 将本地代码推送到指定的远程分支
`git push origin 远程分支名`

**git push的参数**

* **将本地分支与远程分支建立追踪关系，以后推送代码就可以直接使用`git push`，不需要再手动选择远程分支了**
1. `git push -u origin 远程分支名`这个是`git push --set-upstream origin 远程分支名`的简写
2. 已经创建好的分支手动建立联系的方式：`git branch --set-upstream-to=origin/远程分支名 本地分支名`（若本地分支名省略，就默认为当前分支）
3. 创建分支时就建立联系：`git checkout -b 新分支名 origin/线上分支名`

* 强制推送，就算本地分支代码比线上版本更低也能推上去（慎用）
`git push --force origin 远程分支名`简写：`git push -f`(若建立了追踪关系可省略远程分支名)

**git pull和git fetch的区别**
git pull：直接将当前本地分支关联的远程仓库的分支拉取并合并到本地
git fetch：将远程仓库的所有新的提交和分支拉取到本地，但不会自动合并，此时使用`git merge origin/branchName`即可将远程分支合并到本地。
如果有新的分支，则还是需要先创建本地分支，然后再使用`git merge`合并，但第一次推送改代码时需要使用`-u`参数建立远程链接；或者创建分支时直接以远程为基准，`git checkout -b dev origin/dev`，这样使用`git push`时也不需要指定远程分支
`git push`相关的命令涉及远程分支时，origin和分支中间用空格分隔，比如`git push origin master`
`git checkout`相关的命令涉及远程分支时，origin和分支中间用正斜杠分隔，比如`git checkout -b dev origin/dev`

## stash
> 将代码保存，无需commit操作就可以切换到其它分支，还能将保存的代码还原到工作区

* 保存代码，save参数可以添加额外的说明信息
`git stash`
`git stash save 'test'`
* 查看已经保存的代码列表
`git stash list`
* 恢复最后一次保存的代码，并删除这条保存内容
`git stash pop`
* 恢复保存的代码，但不删除保存内容，不加参数默认恢复最后一次保存的代码，可指定恢复某次保存内容
`git stash apply`
`git stash apply stash名字`比如：`git stash apply 1`
* 查看保存的内容和当前目录的差异，可指定查看某条记录，可查看具体差异
`git stash show`
`git stash show stash名字`
`git stash show stash名字 -p`
* 移除指定的保存内容
`git stash drop stash名字`
* 清空所有保存内容
`git stash clear`

如果保存了代码又修改了当前工作区的代码，可能会导致恢复失败，所以应该尽量避免保存后去修改。
这个命令的使用场景主要还是需要临时切换到其他分支开发但是又不想提交代码的情况

## 给git设置和取消代理
在我们在访问github的时候，速度非常慢，我是通过连接vpn来解决速度慢的问题，但是vpn会在电脑上自动开启一个代理，我的电脑是127.0.0.1:1080，(windows10可以在：设置>网络和Internet>代理 进行查看)，此时我们访问github速度就非常快，但是pull和push代码却经常超时，罪魁祸首就是这个代理。我们可以通过给git设置代理来解决拉代码的问题。以下均以127.0.0.1:1080来演示，具体的代理地址请自行查看自己电脑的设置

**查看git代理**
1. 查看全部git配置，包含了代理配置，在windows命令行输入:
`git config --global -l`
如果已经设置了代理，就会看到有`http.proxy = 127.0.0.1:1080`，如果未设置代理，则不会有这一项
1. 只查看代理这一个配置
`git config --global --get http.proxy`
若设置了代理就会看到命令行输出`127.0.0.1:1080`，如果未设置代理，则不会有任何输出

**设置git代理**
* 设置代理只需要使用`git config --global http.proxy 127.0.0.1:1080`即可

**删除git代理**
* 取消代理使用`git config --global --unset http.proxy`

# linux命令
