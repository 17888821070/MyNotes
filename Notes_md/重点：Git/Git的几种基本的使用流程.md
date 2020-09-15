## 从github上拉取项目合并到本地分支

三种方法：

### 一 直接pull远程分支到当前分支，直接覆盖

* 不推荐，会直接覆盖掉本地代码

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

* 不推荐这种方式，还需要额外对临时分支进行处理。

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
$ git fetch origin master:master1  [示例1：在本地建立master1分支，并下载远端的origin/master分支到master1分支中]
$ git fetch origin dev:dev1[示例1：在本地建立dev1分支，并下载远端的origin/dev分支到dev1分支中]
```

**步骤6：**

```
//查看版本差异
$ git diff master1 [示例1：查看本地master1分支与当前分支的版本差异]
$ git diff dev1    [示例2：查看本地dev1分支与当前分支的版本差异]
```

**步骤7：**

```
//合并最新分支到本地分支
$ git merge master1    [示例1：合并本地分支master1到当前分支]
$ git merge dev1   [示例2：合并本地分支dev1到当前分支]
```

**步骤8**

```
//删除本地临时分支
$ git branch -D master1    [示例1：删除本地分支master1]
$ git branch -D dev1 [示例1：删除本地分支dev1]
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

