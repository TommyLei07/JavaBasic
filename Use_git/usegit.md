<!-- TOC -->

- [Git的使用](#git的使用)
    - [常用命令](#常用命令)
    - [GitHub远程仓库](#github远程仓库)
- [多人协作下Git的使用](#多人协作下git的使用)
    - [多分支](#多分支)
    - [一些注意点](#一些注意点)
        - [clone pull fetch的区别](#clone-pull-fetch的区别)

<!-- /TOC -->
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


4. fetch/merge
```
error: failed to push some refs to 'https://github.com/GDDXZ/RobotDenso.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
解决方法：  
- 强制推送
```
$ git push -f
```
可以提交，会将 remote 上第一个人的改动冲掉，比较暴力，不太好。

- 正常解决
```
git fetch origin
git merge origin/master 
```
和本地分支合并，之后再 push。  


# 多人协作下Git的使用
我们知道，在企业级开发中，常常是一个多人团队项目。如果这个时候还是单纯的pull/push这样操作话，对于项目迭代开发，版本管理就会带来很大的麻烦。这个时候就要引入分支。

像我平时使用git都是对于个人项目，所有的更改都是一个人提交 ，所有也不会涉及到这部分的内容。但现在进入到企业级项目后，就需要强化这方面的知识。
## 多分支

像上面的操作都是在 master 分支上进行的。但是假如张三先写了功能 A，李四后写了功能 B，现在 master 上有 A、B 两个功能，如果我不想要功能 A 了，master 进行回退的时候，会连带着功能 B 给回退掉。
![](https://img-blog.csdnimg.cn/2019051718400195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pZG5pZ2h0X3RpbWU=,size_16,color_FFFFFF,t_70)

这种情况下就需要引入多分支了。张三和李四分别开一个分支，在各自的分支上进行开发迭代，然后再分别进行 merge 到 master 上。
![](https://img-blog.csdnimg.cn/20190517184020705.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pZG5pZ2h0X3RpbWU=,size_16,color_FFFFFF,t_70)

创建一个分支的命令是 `git checkout -b 分支名` 
`git checkout -b dev` 等于 `git branch dev`+`git checkout dev`

**张三执行了下面的操作**
```
git branch                  # 先看一下自己在哪个分支
git checkout -b zhangsan    # 创建一个名为zhangsan的分支
git add 功能A.txt            # 将写好的功能A代码add
git commit -m"功能A第一版"    # 将写好的功能A第一版代码commit
git add 功能A.txt            # 将写好的功能A代码add
git commit -m"功能A第二版"    # 将写好的功能A第二版代码commit
```
**接下来，张三与 master 进行 merge 操作**
![](https://img-blog.csdnimg.cn/20190517184050585.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pZG5pZ2h0X3RpbWU=,size_16,color_FFFFFF,t_70)

**现在我们在 zhangsan 分支上，需要先回到 master 分支**
```
git checkout master                                        # 回到master分支
git merge --no-ff -m"merge 功能A with --no-ff" zhangsan     # 在master上执行merge操作
git push origin master   
```
**接下来，李四与 master 进行 merge 操作**
![](https://img-blog.csdnimg.cn/20190517184128727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pZG5pZ2h0X3RpbWU=,size_16,color_FFFFFF,t_70)
发现可以进行 merge，但是没法进行 push，是因为远程的代码有张三 push 的代码，而李四的本地还没有那些代码，需要先执行一下 pull 操作。
执行完 pull 操作后，就能 push 上去了。


## 一些注意点
### clone pull fetch的区别
1. git clone 顾名思义就是将其他仓库克隆到本地，包括被clone仓库的版本变化。举个例子，你当前目录比方说是在 e:/course/ 中，此时若想下载远程仓库，本地无需git init, 直接 git clone url（url 是你远程仓库的地址，直接复制就可以了）。执行 git clone 等待 clone 结束，e:/course/ 目录下自动会有一个.git 的隐藏文件夹（如果看不见，请尝试设置隐藏文件夹可见），因为是 clone 来的，所以.git 文件夹里存放着与远程仓库一模一样的版本库记录。clone 操作是一个从无到有的克隆操作，再次强调不需要 git init 初始化。

2. git pull 是拉取远程分支更新到本地仓库的操作。比如远程仓库里的学习资料有了新内容，需要把新内容下载下来的时候，就可以使用 git pull 命令。事实上，git pull 是相当于从远程仓库获取最新版本，然后再与本地分支 merge（合并）。即：`git pull = git fetch + git merge`
>注：git fetch 不会进行合并，执行后需要手动执行 git merge 合并，而 git pull 拉取远程分之后直接与本地分支进行合并。更准确地说，git pull 是使用给定的参数运行 git fetch，并调用 git merge 将检索到的分支头合并到当前分支中。

```
$ git pull <远程主机名> <远程分支名>:<本地分支名>

$ git pull origin master:branchtest   #将远程主机 origin 的 master 分支拉取过来，与本地的 branchtest 分支合并。
```

3. git fetch 更新远程代码到本地仓库
理解 fetch 的关键，是理解 FETCH_HEAD，FETCH_HEAD 指的是：某个 branch 在服务器上的最新状态’。这个列表保存在 .Git/FETCH_HEAD 文件中，其中每一行对应于远程服务器的一个分支。
当前分支指向的 FETCH_HEAD, 就是这个文件第一行对应的那个分支.
一般来说，存在两种情况:

如果没有显式的指定远程分支，则远程分支的 master 将作为默认的 FETCH_HEAD
如果指定了远程分支，就将这个远程分支作为 FETCH_HEAD

参考：https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6
***未完待续...***