### 本地git仓库的建立

1.设定账号标识(选做)

可以把下面的 xiaoming 改成自己的信息

```
git config --global user.email "xiaoming@qq.com"
git config --global user.name "xiaoming"
```

2.然后 git init 初始化仓库，用 git add 添加到暂存区，再 git commit 提交到版本库，如下：

```
git init
git add *
git commit -m " server class finished "
```

3.后续提交方法

```
git add .
git commit -m "这次新的改动xxxxx"
```

### 上传远程仓库

在自己的github账号里，创建一个新仓库

在本地创建一个main分支(选做)

```
git branch -M main
```

添加远程仓库，并标记为origin，仓库地址替换成自己的

```
git remote add origin https://github.com/picksan/liaotianshi.git
```

推送改动到origin仓库

```
git push -u origin main
git push 仓库名 分支名
```

> -u第一次提交时使用，把本地仓库和远程仓库联系起来，之后提交不需要加u，git push origin main

### git忽略文件

在git本地仓库目录下，创建名为.gitignore的文件

文件内容如下

```
####
#c
#####
*.exe
*.txt
/bin
/temp
```

#包围的是注释

*代表通配符

/bin代表忽略bin文件夹底下的所有文件

### 其他命令

#### 分支操作

查看当前分支

```
git branch
```

合并分支到dev分支

```
git merge dev
```

删除dev分支

```
git branch -d dev
```

切换分支（两种方法）

```
git switch dev
git checkout dev
```

#### 多人协作

查看远程库信息 

```
git remote -v
```

从本地推送分支 ，从本地推送branch-name分支到origin远程仓库

```
git push origin branch-name
```

拉取

```
git pull
```

删除远程仓库

```
git remote rm origin
```

从远程仓库克隆项目到本地

```
git clone 远程仓库地址
```

从远程仓库拉取新分支和数据

拉取远程仓库的分支后，需要合并到当前分支

```
git fetch
git merge
```



#### 版本标签

创建版本标签

```
git tag v1.0
git tag -a v0.1 -m "标签说明"
```

删除标签

```
git tag -d v1.0
```

查看所有标签

```
git tag
```

查看标签内容

```
git show v1.0
```

推送标签

> 标签只存在本地，不会自动推送到远程仓库

推送指定名字的标签

```
git push origin <tagname>
```

推送所有标签到某某远程仓库

```
git push origin --tags
```

删除本地以及远程的标签

```
git tag -d v0.9
git push origin :refs/tags/v0.9
```

#### ssh密钥

查看用户主目录下有没有id_rsa和id_rsa.pub

如果没有创建ssh key

```
#创建ssh密钥和公钥
ssh-keygen -t rsa -C "你的邮箱"
ssh-keygen -t rsa -C "1034161933@qq.com"
```

在github账号里添加你的ssh公钥

之后提交就不用登陆了

#### git的log日志

查看日志

```
# 详细版日志
git log
# 简洁版日志
git log --pretty=oneline
```

### git删除暂存区的文件

```
git rm --cache 文件名
```

### 删除错误提交的commit

```
//仅仅只是撤销已提交的版本库，不会修改暂存区和工作区
git reset --soft 版本库ID
//仅仅只是撤销已提交的版本库和暂存区，不会修改工作区
git reset --mixed 版本库ID
//彻底将工作区、暂存区和版本库记录恢复到指定的版本库
git reset --hard 版本库ID
```

### git本地操作

```
# 查看状态
git status
# 撤销工作区改动
git restore file
```

