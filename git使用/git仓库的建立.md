# git仓库的建立

最后我们将这次实验的项目文件提交到 git 的版本库，先设定账号标识，可以把下面的 xiaoming 改成自己的信息，如下：

```bash
git config --global user.email "xiaoming@qq.com"
git config --global user.name "xiaoming"
```

然后 git init 初始化仓库，用 git add 添加到暂存区，再 git commit 提交到版本库，如下：

```bash
git init
git add *
git commit -m " server class finished "
```

![图片描述](https://doc.shiyanlou.com/courses/3573/600404/6e1cbf801ee6236c53fc648f45c42158-0)

