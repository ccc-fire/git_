#### 基础概念

![img](https://pic2.zhimg.com/80/v2-3bc9d5f2c49a713c776e69676d7d56c5_720w.webp)

Remote：远程仓库，能够被分布在不同点的本地仓库修改，比起本地仓库，远程仓库通常旧一些，因此本地仓库修改完之后需要同步到远程仓库

Repository：本地仓库，位于自己的机器，本地仓库保存了被提交过的各个版本，比起工作区和暂存区的内容，它更旧

Index / Stage：暂存区：位于.git目录下的index文件，暂存区会记录 `git add` 添加文件的相关信息（文件名、大小），**不保存文件实体，通过 id 指向每个文件的实体**。暂存区标记了当前工作区中那些内容是被 git 管理的，当完成某个需求或者功能后需要提交代码，第一步就是通过 git add 先提交到暂存区

Workspace：工作区：进行开发改动的地方，是当前看到的，内容也是最新的，平常开发就是拷贝远程仓库中的分支，基于该分支进行开发，在开发的过程就是在工作区的操作

工作流程：在工作区添加修改文件，通过`git add`将修改后的文件放入暂存区，然后通过`git commit`将暂存区的文件提交到本地仓库，最后通过`git push`将本地仓库的修改推进远程仓库

#### 语法

大致语法：

```
git init	:用于在当前目录中初始化一个新的空白Git仓库,在当前目录创建了一个.git子目录
git add		:添加文件的相关信息到暂存区
git commit	：同步index的目录树到本地仓库
git status	:查看暂存区的状态
git push	:同步本地仓库到远程仓库
```

语法的使用：

##### 1，git add <file>	

将某个文件添加到暂存区，其中file可以是文件名，目录名，或是通配符指定多个文件

```
git add README.md	
//添加单个文件README.md

git add .	
//通配符添加多个文件

git add -A	
//通过-A参数添加所有变动的文件，包括新建的、修改的和删除的文件
```

##### 2，git commit

在提交文件改动时进行不同的操作，双引号中可以附带提交信息，记录提交者对代码或项目所做的修改的摘要描述，没有实际操作性

```
git commit -m "Fix a bug in login feature"	
//将改动的文件添加到暂存区，附带的提交信息是修复漏洞

git commit -a -m "Update README file"	
//将工作目录中已跟踪（tracked）的文件的修改也提交到本地仓库，可以使用 -a 选项

git commit --amend	
//需要修改最近一次提交的提交信息或者添加遗漏的文件改动，这将会将当前的暂存区内容和最近的一次提交合并，并覆盖原来的提交记录。

git commit --allow-empty -m "Empty commit"	
//提交一个空的提交记录（没有具体的文件改动）
```

git commit --amend使用举例：

```
git add file1.txt
git add file2.txt
git commit -m "Initial commit"

//修改提交信息，将 "Initial commit" 修改为 "Initial commit with typo fix"，执行以下命令

git commit --amend	

/*该命令打开默认的文本编辑器（Vim），编辑器将显示最新的提交信息，例如 "Initial commit"，你可以修改最新一次提交的提交信息为 "Initial commit with typo fix"，然后关闭编辑器*/


//添加遗漏的文件

git add file3.txt
git commit --amend

/*将把 file3.txt 添加到上一次的提交中，不会生成一个新的提交记录*/
```

注意：如果你**已经将之前的提交推送到远程仓库**，对最新的提交修改后使用`git commit --amend` 可能会引起问题，因为它改变了提交的 SHA-1 标识符。在这种情况下，最好避免修改已经推送的提交，以免产生不一致的状态。

SHA-1 标识符：每个提交都有一个**唯一**的 SHA-1 标识符，用于唯一标识该提交，使得 Git 能够追踪和管理版本历史。这个标识符是通过将提交的内容计算为一个 SHA-1 哈希值而生成的

##### 3，git status

用于查看当前代码仓库的状态，了解到以下信息：未跟踪的文件（Untracked files），已修改的文件（Modified files），已暂存的文件（Staged files），当前分支信息（Branch information）

未跟踪的文件：这些是在 Git 仓库中但尚未被 Git 跟踪的新文件，它们没有被包含在任何提交中，包括未被git初始化的文件和已被git忽略的文件

已修改的文件：这些文件已经被 Git 跟踪，并且在最后一次提交后再进行了修改，**没有提交修改**，这些修改尚未被包含在新的提交中

已暂存的文件：这些文件已经被 Git 跟踪，并且已经**通过 git add命令将其添加到暂存区**（Git 的一个中间状态）

当前分支信息：`git status` 还会告诉你当前所在的分支，以及与该分支相关联的远程分支

##### 4，git push <remote> <branch>

用于**将本地分支的提交推送到远程仓库**，其中`remote`是远程仓库的名称，可以是一个 URL （用于定位和访问互联网资源的地址）或者一个在本地配置的远程仓库的别名，比如 `origin`；`branch`是要推送的分支的名称

```
git push -u <remote> <branch>
//用于将本地分支推送到远程仓库并建立追踪关系

git push origin master	
//将当前分支的提交推送到远程仓库的同名分支，具体解释是将把当前分支（通常是 master 分支）的提交推送到名为 origin 的远程仓库的 master 分支上

git push --all origin	
//推送所有分支的提交到远程仓库，将推送所有本地分支的提交到名为 origin 的远程仓库

git push --force origin branch-name		
//强制推送分支的提交，这将强制推送指定分支的提交，覆盖远程仓库上已存在的提交

```

git push -u <remote> <branch>使用举例：

```
git push -u origin main
//将当前分支的更新推送到名为 origin 的远程仓库，并与远程仓库的 main 分支建立追踪关系
//以后就可以使用 git push 和 git pull 来推送和拉取与远程仓库建立追踪关系的当前分支的更新
```

##### 5，git pull <remote> <branch>

用于从远程仓库获取**最新**的提交并**合并**到当前分支,它相当于执行了` git fetch`后再执行 `git merge`

```
git pull origin main
//从名为 origin 的远程仓库的 main 分支获取最新的提交信息，并将其合并到当前分支（main 分支）中
```

##### 6，git fetch <remote>

从远程仓库获取**最新的提交**，但不会自动合并到当前分支。它只是将远程分支的更新保存到本地，你可以根据需要选择是否进行合并

```
git fetch origin
//从名为 origin 的远程仓库获取最新的提交信息，但不会直接合并到当前分支
```

##### 7，git merge <branch>

用于将分支的更改合并到当前分支中

```
//假设有两个分支：feature 分支和 main 分支。现在我们想将 feature 分支的更改合并到 main 分支中。

//切换到 main 分支：
git checkout main

//从远程仓库更新最新的提交信息：
git fetch origin

//合并 feature 分支到 main 分支：
git merge feature
```



##### 8，git clone <repository> [<directory>]

用于克隆远程仓库到本地。它会创建一个与远程仓库相同的副本，并在本地建立追踪关系。其中 `<repository>` 是要克隆的远程仓库的 URL，可以是 HTTP 或 SSH 链接；`<directory>` 是可选参数，表示要将代码库克隆到的目录名称。如果不指定 `<directory>`，则默认以远程仓库的名称作为目录名称进行克隆

```
git clone https://github.com/example/repo.git
//克隆名为 repo 的远程仓库到当前目录，并创建一个名为 repo 的文件夹来存放克隆下来的代码
```

##### 9，git checkout

用于切换分支或恢复文件。它可用于切换到已存在的分支、创建新分支，或者还原文件为之前某个提交的状态。

- 切换到已存在的分支：

  ```
  git checkout <branch>
  ```

- 创建新分支并切换到该分支：

  ```
  git checkout -b <new_branch>
  ```

- 还原文件为上一个提交的状态：

  ```
  git checkout -- <file>
  ```

##### 常用语法总结：

```
git init                 	      #把当前目录变成git可以管理的仓库
git clone https         		  #克隆项目
git add readme.txt        		  #添加一个文件，也可以添加文件夹
git add .               	      #添加全部文件
git rm test.txt                   #删除一个文件，也可以删除文件夹
git commit -a -m “some commit”    #提交修改
git status                        #查看状态，检查是否还有未提交
git log                           #查看最近更新日志
git reset --hard HEAD^            #版本回退一个版本
git reset --hard HEAD^^           #版本回退两个版本
git reset --hard HEAD~100         #版本回退多个版本
git remote add origin 远程仓库地址  #将远程仓库和本地仓库进行关联
git push -u origin master         #将本地仓库的提交推送到远程仓库中的主分支
git push                          #远程仓库的提交（第二次及之后）
git fetch                         #从远程获取最新的提交历史和文件，不自动合并
git tag xxx                       #打tag
git tag                           #显示所有tag
git push --tag                    #提交tag
git branch -a                     #显示所有分支
git checkout 分支名                #切换分支
git merge git分支                  #合并分支
git branch						  #查看分支
git branch name					  #创建分支
git branch -b name				  #创建并切换到该分支
git merge name					  #合并某分支到当前分支
git branch -d name				  #删除分支
git diff						  #比较工作区与暂存区的区别
```

#### 实操

##### 1，添加远程仓库

```
git remote		//查看远程库的信息
git remote -v		//显示更详细的信息

git remote add origin 远程仓库地址	//将本地仓库与远程仓库进行关联

git push -u origin maser	
//将本地库的内容推送到远程，-u使得Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令

git push origin master	//把本地master分支的最新修改推送至GitHub
git push origin 本地分支名称:远程分支名称	//将本地分支推送到远程仓库的相应分支

//常见问题

//本地分支和远程分支的提交历史没有共同的祖先，导致无法自动合并
git pull origin master --allow-unrelated-histories		//强制允许合并没有共同祖先的提交历史

//本地的分支落后于远程分支
git pull origin master	//拉取远程分支上的最新更改，并尝试合并到本地分支

//添加的时候地址写错了，或者就是想删除远程库
git remote -v	//查看远程库信息
git remote rm origin	
//删除远程库origin，此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库

//他人从远程库中下载除主分支以外的分支（dev）
git checkout -b dev origin/dev
git add file
git commit -m ""
git push origin dev

//当你和他人都要推送更新好的dev分支，发生冲突，首先git pull把最新的提交抓下来，合并在本地之后再推送
git branch --set-upstream-to=origin/dev dev		//设置dev和origin/dev的链接
git pull  		//抓最新的提交
git push origin branch-name		//推送自己的修改
```

其中*origin*是一个常用的用来表示默认远程仓库的别名，也可以选择自定义其他别名来表示不同的远程仓库

##### 2，从远程库克隆

```
git clone 远程仓库地址	//克隆远程仓库

//常见问题

//目标路径已经存在，不是一个空目录
//方法一，删除该目录，重新克隆

rm -rf name
git clone https地址

//方法二，将远程仓库的内容合并到现有的目录中

cd name
git pull origin master
```

##### 3，管理修改

```
git reser --hard HEAD^		//上两个版本就是^^,上100个版本就是HEAD~100

//没关闭命令窗口，恢复到某个版本
git log --pretty=oneline	//查看版本号
git reser --hard 版本号	  //取版本号的前四位即可

//关闭了命令窗口
git reflog	//记录每一次命令
git reser --hard 版本号
```

##### 4，撤销修改

```
//还未放入暂存区
git checkout -- file	//撤销修改就回到和版本库一模一样的状态

//已放入暂存区
git reset HEAD <file>	//将暂存区的修改退回到工作区
git checkout -- file	//
```

##### 5，删除文件

```
rm file		//直接在工作区删除file与该命令效果一样

//从版本库中删除该文件
git rm file	
git commit -m "  "

//删错了,把误删的文件恢复到最新版本
git checkout -- file
//注意：从来没有被添加到版本库（git commit）就被删除的文件，是无法恢复的！
```

##### 6,创建与合并分支

```
git checkout -b dev
git branch	//当前分支前面会标一个*号
git checkout master
git merge dev	//fast-forward表示这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快

//注意，切换分支用switch更科学
git switch master
```

##### 7，解决冲突

```
//两个分支都有各自的新的提交，无法快速合并
cat file	//查看冲突的地方，将文档改为HEAD的内容
git add file
git commit -m "  "
cat file	//查看冲突是否解决
git log --graph	  	//查看分支合并图
```

##### 8，分支管理策略

强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息

```
 git merge --no-ff -m "merge with no-ff" 分支名称	
 git log --graph --pretty=oneline --abbrev-commit	//用一行简洁的格式展示每个提交的信息
```

注意：合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并

##### 9，Bug分支

创建新的分支来修复bug

```
//假设再dev分支进行工作
git stash		//将目前的工作先储藏在当前分支中
git checkout master
git checkout -b issue-101	//创建bug分支，将bug手动修改后再进行下列操作
git add file
git commit -m "  "
git checkout master
git merge --no-ff -m "合并并且进行提交" issue-101		//bug修复完成后继续工作
git checkout dev
git stash list	//查看最开始储藏起来的工作现场

//开始恢复工作现场
//方法一
git stash apply		//恢复后，stash内容并不删除
git stash drop		//删除stash内容

//方法二
git stash pop		//恢复的同时把stash内容也删了

//恢复成功后
git stash list		//查看stash内容已经清空

//dev分支是从master中分出来的，其中有着与master相同的bug，如何更快的修复？
git cherry-pick <commit>		//找到提交的commit版本号
```

##### 10，rabase

把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了

```
git rebase
git push origin master
git log --graph --pretty=oneline --abbrev-commit
```

##### 11,标签管理

```
git branch
git checkout master
git tag 标签名		//给主分支打标签
git tag		//查看标签
git log --pretty=oneline --abbrev-commit	//查看历史提交的commit id
git show tagname	//查看标签信息
git tag -a tagname -m "   " commitversion	//创建带说明的标签
git push origin <tagname>	//可以推送一个本地标签；
git push origin --tags		//可以推送全部未推送过的本地标签；
git tag -d <tagname>		//可以删除一个本地标签；
git push origin :refs/tags/<tagname>		//可以删除一个远程标签。

```





#### 知识点：

HEAD和分支

HEAD:指向当前所在分支（例如主分支master）或指向具体的提交（也就是分离头指针），master才是指向提交的

分支：提交会添加到当前所在分支指向的提交之后，并移动该分支指针指向最新的提交

创建一个新的分支，在新的分支dev修改内容，将内容完善之后，再合并到mater分支中，也就是master指针也指向最新的一次提交，合并之后，dev分支就可以删除了

