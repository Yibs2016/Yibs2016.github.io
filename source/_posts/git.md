---
title: git
---
### 概念
源码管理系统
对当前文件提供版本管理功能，核心思想是对当前文件建立一个对象数据库，将历史版本信息存放在这个数据库中

### config
git config –global user.name xxx      //不要加引号
git config –global user.email xxx
git config --global core.ignorecase false  //默认忽略大小写
git config --global credential.helper store  //凭证存储  cache 15分钟后从内存中清除 store 将凭证用明文的形式存放在磁盘中永不过期
本地私钥，远程存放公钥
#### 步骤
1.生成ssh密钥
    1. Git Bash
    2. ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
2.添加到账户
    1. .ssh文件夹下-复制id_rsa.pub 
### 快照
二进制对象:保存当前目录结构,以及文件信息
### 指针
分支指针是`` 动态 ``的,改写分支指针的命令有：
1. git commit
2. git pull
3. git reset 
### branch
执行某个快照的指针,分支名就是指针名;当前分支有新快照,指针就会自动指向它
`` HEAD ``特殊指针,指向当前分支最近一次快照 
`` HEAD^ ``HEAD前一个快照 
`` HEAD~6 ``HEAD之前的第6个快照 
git push origin --delete xxx  //删除远程分支

<!--more-->
### 合并
dev靠前,master靠后，master可以通过merge追赶上dev: git merge dev 
- --ff 直进式合并，不生成单独的合并节点[默认]
- --no-ff 非直进式，生成单独节点，利于保持清晰的cm信息
- --squash 合并所有commit history作为本地的修改，需要手动commit，可以保持 master 分支干净
  [不提交、不移动HEAD，因此需要一条额外的commit命令]
- --abort 终止合并

### detached HEAD 游离态
HEAD不指向本地某分支都处于该状态
- co到某个cm,且那个cm目前没有分支指向它
- rebase是不断detached HEAD的过程

### 追踪关系
某些场合，Git会自动在本地分支和远程分支之间建立一种追踪关系，比如git clone，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说本地master自动追踪origin/master分支
 
### hook
发生特定行为时机,触发执行自定义脚本
pre-commit: 执行git commit命令时触发，常用于检查代码风格  //"lint-staged"
commit-msg: commit后触发，常用于校验提交说明是否标准  //"commitlint -E HUSKY_GIT_PARAMS"
post-commit: 整个git commit完成后触发，常用于邮件通知、提醒
post-merge: 成功完成一次 merge行为后触发
pre-push: 执行git push命令时触发，可用于执行测试用例
pre-auto-gc: 执行垃圾回收前触发

### cherry-pick
命令"复制"一个提交节点并在当前分支做一次完全一样新提交
cp多个提交需要按照提交顺序先到后，逐个嶙选
cherry-pick不一定提交的hash,分支名也是可以的[转移改分支最新提交]
1. 转移系列提交 git cherry-pick A..B [提交A必须早于提交B,不包含A]   git cherry-pick A^..B[包含A] 
2. -m
   如果原始提交是个合并节点，来自2分支的合并，cp默认失败[不知道应该采用哪个分支的代码变动]
   -m告诉git应该采用哪个分支的变动,参数parent-number为从1开始的整数: 原始提交的父分支编号
   git cherry-pick -m 1 <commitHash> --> 采用提交commitHash来自编号1的父分支的变动
   1号父分支: 接受变动的分支
   2号父分支: 变动来源分支
   比如xxx分支合到dev,cp -m 1 id 到master,合并的是dev分支的变动; cp -m 2 id 到master,合并的是xxx分支的变动;
  

### revert
用于撤销commit
1. git revert commit_id  [按距离现在时间近到远一次cm]
2. git revert多个连续cm
    git revert 5...3  不包含3   5->4 依次回退-> [ )
    git revert 3...5 [也可，不会包含3]
### fetch
取回远程所有更新
所取回的更新,在本地主机上要用"远程主机名/分支名"的形式读取;如origin/master
### rebase 
变基,把提交历史整理成竖线,更直观
所取回的更新,在本地主机上要用"远程主机名/分支名"的形式读取;如origin/master
### 修改文件/文件夹大小写
#### 走的弯路
1. 直接修改名字后提交 
2. 删除后commit,再将改好大写的备份文件add回去 
3. 直接mv大写文件
#### 正解
1. git mv ./Docs ./docs.bak  git commit -m "rename step1"
2. git mv ./docs.bak ./Docs git commit -m "rename step2"   
3. git push
4. [注]：不能重命名正在开发中的文件夹,不然合并时冲突会导致重命名失效
### 删除文件/文件夹 
1. git rm  
2. git rm -r

#### 删除分支
##### 删除远程分支
1. 删除远程分支 git push origin --delete xxx  
    gitlab手动删除后本地还有备份数据，用指令 git remote prune origin 同步 
    [= git fetch -p = git remote update -p]
2. 删除本地分支 
    [已推送或合并]git branch -d xxx
    [未推送]git branch -D xxx
  
#### 合并多次commit
1. 查看commit节点id: git log --oneline
2. git rebase -i [commit_id]   [追踪到该id的上一条]
  - 进入rebase界面后，i,修改pick为squash[第一个pick除外] 
   -- pick cm1
   -- pick cm2
    * cm1早于cm2提交,必须第一个为pick,后面为squash
    * squash的作用是把commit合并到上一个提交
  - esc+:+wq回车保存
    * 保存后会进入另一个vi界面，如果第一个cm信息不想要保留，可以该界面修改