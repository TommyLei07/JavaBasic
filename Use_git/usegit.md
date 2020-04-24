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
<div align="center" style="display: none;">
<img src="../img/gitstatus.jpg" width="70%" >
</div>
<div align="center">
<img src="../img/gitstatus.jpg" width="70%" >
</div>  

[comment]: <> ( " ![gitstatus](../img/gitstatus.jpg "git status" )  
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


