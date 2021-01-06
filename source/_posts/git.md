---
title: git
---
### config
git config –global user.name 'xxx'
git config –global user.email 'xxx'
git config --global core.ignorecase false  //默认忽略大小写
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

<!--more-->
### fast forward
dev靠前,master靠后，master可以通过merge追赶上dev,追赶动作即为fast forward
- git merge –no-ff dev 保存dev历史,方便查看提交记录
- git merge dev 只保留单分支记录
### detached HEAD 游离态
HEAD不指向本地某分支都处于该状态
- co到某个cm,且那个cm目前没有分支指向它
- rebase是不断detached HEAD的过程
### hook
发生特定行为时机,触发执行自定义脚本
pre-commit: 执行git commit命令时触发，常用于检查代码风格  //"lint-staged"
commit-msg: commit后触发，常用于校验提交说明是否标准  //"commitlint -E HUSKY_GIT_PARAMS"
post-commit: 整个git commit完成后触发，常用于邮件通知、提醒
post-merge: 成功完成一次 merge行为后触发
pre-push: 执行git push命令时触发，可用于执行测试用例
pre-auto-gc: 执行垃圾回收前触发

 
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

