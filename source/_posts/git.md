1.在代码文件夹右键GitBash Here

2.Git仓库初始化：  git init

3.将文件夹中代码添加到仓库： git add .

4.提交  git Commit  -m “说明”

5.建立分支 git  branch  Testbranch（该分支是基于master创建的，所以只有做完步骤3、4才可以建立分支）

6.切换到新建的分之不上 ：gitcheckout Testbranch

7.添加远程仓库链接：
git remote add origin git@github.com:nanfei9330/learngit.git




删除远程分支
$ git push origin :test 

提交到远程新分支
$ git push origin newbranch

提交本地test分支作为远程的master分支
$ git push origin test:master         
