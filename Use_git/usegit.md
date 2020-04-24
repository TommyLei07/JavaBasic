# Git的常用命令
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
![gitstatus](../img/gitstatus.jpg "git status")