作用： 版本管理和多人协同控制

# 仓库
1. 初始化仓库
```text
git init
```
2. 查看仓库状态
```text
git status
```
如果工作区里的文件没有没添加，就会显示untracked files，我们可以通过add指令添加到暂存区。
3. 添加文件到仓库的暂存区
```text
git add .
```
4. 将暂存区文件放到仓库的分支下
```text
git commit -message "add two files"
```

5. 连接远程仓库
在github上新建一个仓库会产生一个SSH，然后通过git命令连接到github的仓库
```text
git remote add origin https://github.com/cx748/First-Repository.git
```
6. push本地仓库的文件到远程仓库
```text
git push -u origin master
```
当远程仓库是空的时候，第一次push master需要增加一个-u参数。这样Git不仅仅会把本地的master分支的内容推送到远程GitHub仓库中新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送和拉取就可以简化命令了
7. 下载clone仓库
```
git clone https://github.com/cx748/First-Repository
```text
8 pull拉取仓库内容
```text
git pull origin master
```

# 分支
分支记录了每一次提交的仓库文件状态
每个分支都有一个指针，新建一个分支，首先是新建一个指针，而且新分支的的指针会和当前的分支指向同一个提交点。
1. 查看当前分支
```
git branch
```
2. 创建分支
```
git branch dev
```
3. 切换分支
```
git checkout dev
```
4. 分支提交日志
```
git log --oneline
git log
```
5. 分支合并
```
git merge master(分支名)
```
 1. 快速合并
 当A分支没有新的提交点时，git merge B，会把A分支的指针指向B分支的当前提交点。
 2. 三方合并
 当AB分支都有新的提交点时，将A,B的更改都累加到A、B分支共同的提交点上，并创建新的提交点。

6. 合并冲突
两个分支在进行合并时，但他们含有同一个文件的修改，则在合并时出现冲突，git无法决定该保留哪个分支的修改。
