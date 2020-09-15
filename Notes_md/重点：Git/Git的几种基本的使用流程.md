## 李籽兴总结的工作流程，从无开始

- 准备工作，拿到远程代码

1、创建本地仓库

> $ git init 

2、链接Github

> $ git remote add origin https://github.com/17888821070/MyNotes.git

3、拉取分支到本地

> $ git fetch

4、拉取远程主分支（origin master）到本地（master）

> $ git pull origin master

5、创建本地开发分支（development），并切换

> $ git branch development
>
> $ git checkout development

6、拉取远程开发（origin development）分支到本地（development）

> $ git pull origin development

- 创建本次开发的分支，所有修改现在此分支上完成

7、从本地开发分支创建本次维护的分支（lizixing），并切换

> $ git branch lizixing
>
> $ git checkout lizixing

8、在lizixing上进行代码修改

> $ git add .
>
> $ git commit .

- 完成本次开发后，想要提交本次修改内容到远程

9、重新拉取远程开发（origin development）分支到本地（development）

> $ git checkout development
>
> $ git pull origin development

10、查看新拉取的项目和个人开发的lizixing分支内是否有冲突

> $ git diff lizixing 或者 git diff --name-only lizixing 

11、存在冲突时，手动处理冲突

> 协商后使用beyond compare等工具修改lizixing分支或development分支代码

12、合并lizixing分支到development分支

> git merge lzixing

13、上传development分支

> git push origion development

14、删除开发分支

> git branch -d lizixing





## 从github上拉取项目合并到本地分支

三种方法：

### 一 直接pull远程分支到当前分支，直接覆盖

- 不推荐，会直接覆盖掉本地代码

步骤1：

```
//初始化一个Git仓库
$ git init
```

步骤2：

```
//链接远程仓库
$ git remote add origin https://github.com/17888821070/MyNotes.git
```

步骤3：

```
// 从远程获取代码库
git fetch
```

步骤4：

```
//查看远程有哪些分支
$ git branch -a
```

步骤5：

```
//拉取远程指定分支到当前分支
$ git pull origin master
```

步骤6：

```
//创建分支
$ git branch 重点：Git
//切换分支
$ git checkout 重点：Git
//拉取远程指定分支到当前分支
$ git pull origin 重点：Git
```

步骤7：

```
//修改内容，可以自己创建本地分支，最终再合并到此分支
$ touch readme.txt
//add并commit
$ git add.
$ git commit -m "xxxx"
```

步骤8

```
//上传至远程指定分支
$ git push origin 重点：Git
```



### 二 使用fetch和merge的方式，创建临时分支

- 不推荐这种方式，还需要额外对临时分支进行处理。

步骤1：

```
//初始化一个Git仓库
$ git init
```

步骤2：

```
//链接远程仓库
$ git remote add origin https://github.com/17888821070/MyNotes.git
```

步骤3：

```
// 从远程获取代码库
git fetch
```

步骤4：

```
//查看远程有哪些分支
$ git branch -a

```

**步骤5：**

```
//获取最新代码到本地临时分支(本地当前分支为[branch]，获取的远端的分支为[origin/branch])
$ git fetch origin master:master1 //本地创建master1分支，获取远程master分支内容到此新分支
$ git fetch origin 重点：Git:Git1 //本地创建Git1分支，获取远程重点：Git分支内容到此新分支

```

**步骤6：**

```
//查看版本差异
$ git diff master1 [示例1：查看本地master1分支与当前分支的版本差异]
$ git diff Git1    [示例2：查看本地Git1分支与当前分支的版本差异]

```

**步骤7：**

```
//合并最新分支到本地分支
$ git merge master1    [示例1：合并本地分支master1到当前分支]
$ git merge Git1   [示例2：合并本地分支Git1到当前分支]

```

**步骤8**

```
//删除本地临时分支
$ git branch -D master1    [示例1：删除本地分支master1]
$ git branch -D Git1 [示例1：删除本地分支Git1]

```

步骤9

```
//上传至远程指定分支
$ git push origin 重点：Git

```

### 三 使用fetch和merge的方式，不创建临时分支

- 推荐这种方式

步骤1：

```
//初始化一个Git仓库
$ git init

```

步骤2：

```
//链接远程仓库
$ git remote add origin https://github.com/17888821070/MyNotes.git

```

步骤3：

```
// 从远程获取代码库
git fetch

```

步骤4：

```
//查看远程有哪些分支
$ git branch -a

```

**步骤5：**

```
//获取最新代码到本地(本地当前分支为[branch]，获取的远端的分支为[origin/branch])
$ git fetch origin master  [示例1：获取远端的origin/master分支]
$ git fetch origin dev [示例2：获取远端的origin/dev分支]

```

**步骤6：**

```
//查看版本差异
$ git log -p master..origin/master [示例1：查看本地master与远端origin/master的版本差异]
$ git log -p dev..origin/dev   [示例2：查看本地dev与远端origin/dev的版本差异]

```

**步骤7：**

```
//合并最新代码到本地分支
$ git merge origin/master  [示例1：合并远端分支origin/master到当前分支]
$ git merge origin/dev [示例2：合并远端分支origin/dev到当前分支]

```

步骤9

```
//上传至远程指定分支
$ git push origin 重点：Git

```





> 