<!-- TOC -->autoauto- [Git的使用](#git的使用)auto    - [常用命令](#常用命令)auto    - [GitHub远程仓库](#github远程仓库)autoauto<!-- /TOC -->
# Git的使用
## 常用命令
1. 创建仓库  
```
/*
先在本地某一个位置创建一个文件夹作为你的仓库目录
然后进入该目录，使用init命令即可完成创建
*/
$ mkdir gitfolder   
$ cd gitfolder
$ git init
```
2. 添加命令
```
$ git add filename //添加指定文件
$ git config/*     //config 目录下及子目录下所有文件
$ git home/*.php   //home 目录下的所有.php 文件
$ git add 文件夹名
$ git add --all    //添加所有文件
```
3. 提交命令
```
$ git commit -m "add new file"
```
4. 查看状态
```
$ git status
```
<div align="center">
<img src="../img/gitstatus.jpg" width="70%" >
</div>  
 
5. 查看修改
```
$git diff filename
```

6. 查看日志
```
$ git log
```
7. 回退  
 Git 在内部有个指向当前版本的 HEAD 指针
```
$ git reset --hard 1094a
$ git reset --hard HEAD^
```
8. 查看命令历史
```
$ git reflog
```
9. 删除文件、恢复文件
```
$ rm filename
$ git rm filename
$ git commit -m "commit后从仓库中完成删除"

$ rm filename
$ git checkout -- filename //从版本库中恢复
```

## GitHub远程仓库
1. 生成SSH key
```
ssh-keygen -t rsa -C "youremail@example.com"
```
<div align="center">
<img src="../img/sshkey.jpg" width="70%" >
</div>  
然后将那一串key添加到GitHub账户的ssh key中

2. 添加远程仓库
```
$ git remote add origin git@github.com:accountname/project.git
```
把本地仓库和远程仓库进行关联
*这里遇到的问题：'fatal:remote origin already exists'*  
*解决方法：*
```
$ git remote rm origin
$ git remote add origin git@github.com:accountname/project.git
```

3. 推送
```
$ git push -u origin master
```

***未完待续...***