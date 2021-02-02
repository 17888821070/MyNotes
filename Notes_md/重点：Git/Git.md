# Git

## 一、概述

### Git以快照形式存储版本历史

Git 更像是把数据看作是对小型文件系统的一系列快照。 在 Git 中，每当你提交更新或保存项目状态时，它基本上就会对当时的全部文件创建一个快照并保存这个快照的索引。 为了效率，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 Git 对待 数据更像是一个 快照流。

### Git保证完整性

Git 中所有的数据在存储前都计算校验和，然后以校验和来引用。 这意味着不可能在 Git 不知情时更改任何文件 内容或目录内容。 这个功能建构在 Git 底层，是构成 Git 哲学不可或缺的部分。 若你在传送过程中丢失信息或损 坏文件，Git 就能发现。

Git 用以计算校验和的机制叫做 SHA-1 散列(hash，哈希)。 这是一个由 40 个十六进制字符(0-9 和 a-f)组 成的字符串，基于 Git 中文件的内容或目录结构计算出来。 SHA-1 哈希看起来是这样:

```
  24b9da6552252987aa493b52f8696cd6d3b00373
```

Git 中使用这种哈希值的情况很多，你将经常看到这种哈希值。 实际上，Git 数据库中保存的信息都是以文件内 容的哈希值来索引，而不是文件名。

### Git保存的内容

Git保存的是各个版本的快照，当进行提交之后会将有修改的文件的快照保存到本版本，未修改的内容只想历史的索引

### 三种状态

* 已提交
* 已修改
* 已暂存

### 三个阶段

* 工作区
* 暂存区
* Git目录

### 首次运行Git前的配置

* 查看配置

  ```
  git config --list
  ```

* 用户信息

  ```
  git config --global user.name "xxx"
  git config --global user.email xxx@xxx.xxx
  ```

  

## 常用指令

#### 基本本地操作

* git add 

  * 把工作区文件加入到暂存区
  * 标记解决冲突

*  git restore

  * <filename> 撤销修改但未git add 的文件修改
  *  --staged <filename> 撤销git add 但未commit的文件修改

* git reset HEAD <file>

  * 撤销git add

* git commit 

  * 将暂存区标记的文件提交到压缩数据库
  * --amend 重新提交，替代上一次提交，可用于git commit之后发现有漏add文件或者提交信息写错等情况

* git log

  * 查看提交历史
  * -- stat 查看带修改细节的提交历史
  * --pretty=oneline 只显示提交备注和提交的hash

* git status 

  * 查看工作区已修改但未加入到暂存区的文件
  * 查看暂存区跟踪但未加入到git目录的文件

* git diff
  * 查看工作区和暂存区的区别

* git diff --staged 

  * 查看暂存区和git目录的区别

  

#### 远程仓库操作

* git remote
  * 查看远程仓库 
  * -v 查看远程仓库，带url
  * add  <shortname> <url>添加远程仓库，并制定缩写
* git fetch <remotename>
  * 拉取远程仓库到本地仓库（此命令不会合并，不会覆盖本地文件，只会把远程仓库分支拉取到本地仓库）

* git push
  * <remote> <branch>将本地commit推送到<remote>远程仓库的<branch>分支
* git pull 
  * 

#### 分支

* git branch
  * 查看分支列表
  * -v 查看每一个分支的最后提交
  * --merged 查看已合并的分支，查出来的分支是可以合并的，因为已经合并过了
  * -- no-merged 查看未合并的分支
  * <branchname> 创建分支
  * -d <branchname> 删除分支
  * -D <branchname> 强制删除分支

* git checkout 

  * <branchname>分支切换，切换分支时，工作区内容会切换成该分支的快照
  * -b <newbranchname>创建分支并切换到新的分支

* git merge 

  * <branchname> 将branchname分支的内容合并到当前分支（git merge master，将master分支合并到当前分支）

  ```
  merge 冲突解决，当两个分支更改了同一个文件的同一个地方就会出现冲突
  冲突的文件就会显示成
  <<<<<<< HEAD:index.html
  <div id="footer">contact : email.support@github.com</div> 
  =======
  <div id="footer">
  please contact us at support@github.com </div>
  >>>>>>> iss53:index.html
  只能采用其中的一个（删掉另一部分和一些标志文本），或者手动解决冲突
  ```

* 

### 经典分支开发工作流



