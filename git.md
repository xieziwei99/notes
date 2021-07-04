[TOC]



## git 基础命令

### 创建仓库（repository）

1. 创建一个空目录：`mkdir learngit`

2. 初始化仓库：`git init`

   把这个目录变成Git可以管理的仓库，完成后会多一个隐藏目录 .git

### 添加文件到仓库

1. `git add <filename>`
2. `git commit -m <message>`

### 添加远程库

1. 先在 github 上新建一个仓库

2. 将本地仓库与远程仓库关联：

   `git remote add origin git@github.com:xieziwei99/learngit.git`

3. 将本地库内容推送到远程：

   第一次：`git push -u origin master`

   后几次：`git push`

### 从远程库克隆

- 克隆：`git clone git@github.com:xieziwei99/learngit.git`
- 之后获取更新：`git pull`

### 将已经add的文件（未commit）从本地库中移除

```sh
git rm -r --cached .\superlists\__pycache__		# -r表示递归，--cached表示不删除本地文件
```



### 将没有 add 的修改撤销

```sh
git checkout -- a.txt
```



### 如果已经 add 但没有 commit

```sh
git log		# 查看版本号

git reset cc58	# 重置到某个版本号，此时 add 的文件将回到没有 add 的状态
# 或者使用 git reset --hard cc58，此时会重置到某个版本号，并丢掉所有的更改

git checkout -- a.txt
```



### git stage

等同于 git add

