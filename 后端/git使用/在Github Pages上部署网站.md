# 在Github Pages上部署自己的简历

### 一、实验介绍

#### 1.1 实验背景

这几天室友忙着找工作改简历，想到自己也会有这么一天，那么就来一起好好写一份简历吧。

期间在网上找了不少写简历的资源，比如[轻单-在线简历制作](https://qdan.me/list/VUR-PAX01x8Skk0F)收录了一些在线生成简历的网站，有需要的同学可以直接拿走，不用再看这节课啦。

才怪！我对自己写的简历模板有信心，放这个出来就是要比比看。比比看性价比，我们这个模版的价格是 0，分母是 0 就意味着性价比无穷大！

写这个模版的初衷是希望同一份简历既能做页面展示，也能直接打印出来给我到处投。（请认真对待每一份简历，不要学习笔者）。

写这份模版也是站在巨人的肩膀上，参考了前人经验的。感谢以下先辈：

- [一看 star 数就知道一定是最牛的简历](https://github.com/DIYgod/Resume)
- [freepik 上的好看简历](http://www.freepik.com/free-psd/editable-cv-format-download_716578.htm)

我们可以利用 Github 的静态页面托管服务 Github Pages 来帮助我们做页面展示。

> 什么是 Github Pages？

> Github Pages 是 Github 的静态页面托管服务。它设计的初衷是为了用户能够直接通过 Github 仓库来托管用户个人、组织或是项目的专属页面。参考：https://help.github.com/articles/what-is-github-pages/

可以说相当于一个可直接用 git 管理内容的静态服务器，有许多人会用它来托管自己的个人博客（利用 Jekyll、Pelican 这一类静态页面生成工具）或是在这上面发布自己的 HTML5 小游戏。当然这么好的东西也是有限制的。

Github Pages 的限制：

- 仓库存储的所有文件不能超过 1 GB
- 页面的带宽限制是低于每月 100 GB 或是每月 100,000 次请求。
- 每小时最多只能部署 10 个静态网站。

对于发布自己的简历或是部署自己的博客的这一类需求我想是不用担心这些限制的，如果真的不小心超了，Github 那边不会采取什么强制措施，而是会发一份邮件提醒你应该找一个更适合你的托管对象的服务。

#### 1.2 预备知识

本课程需要的预备知识：

- `git` 的基本使用

如果对 `git` 完全陌生，推荐学习实验楼的[《Git 实战教程》](https://www.shiyanlou.com/courses/4)，仅需了解最基本的操作即可。

#### 1.3 实验知识点

本课程项目完成过程中，我们将学习：

- 在 Github Pages 上部署自己的简历

#### 1.4 适合人群

适合对于简历有要求的同学，学习本课程可以完美的让你对简历进行管理

#### 1.5 最终效果

简历页面展示： ![1-1.5-1](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472193368787.png)

保存后的 pdf 版本：

![1-1.5-2](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472193027584.png)

### 二、实验步骤

本次实验我们从初始化Git库开始，编辑简历文件，将文件部署在Github Pages上，最后可以将其保存为pdf格式并打印出来。

下面我们进入具体的实现阶段。

**请尽量按照实验步骤自己操作，请确认文件保存在目录：“/home/shiyanlou/Code” 下。**

#### 2.1初始化git库

打开终端，进入 `Code` 目录，创建 `CV` 文件夹, 并将其作为我们的工作目录。

```
$ cd Code
$ mkdir CV && cd CV
```

初始化 git 库。

```
$ git init
```

用户配置（可选）：

```
$ git config --global user.name "你的用户名"
$ git config --global user.email "你的邮箱地址"
```

这一步不做也没关系，用户名和邮箱是你提交`commit`时的签名，在 Github 的仓库页面上会显示这次提交的用户，如果不做设置就会默认为该仓库的拥有者，做了则根据邮箱来匹配用户。

#### 2.2编辑简历文件

下载页面模板文件。

解包，并且直接将其中的文件置于 CV 文件夹下。

```
$ wget http://labfile.oss.aliyuncs.com/courses/624/cv-template.zip
$ unzip cv-template
$ mv cv-template/* .
$ rm -rf cv-template* __MACOSX*
```

用浏览器打开就可以看见模板的样子了。同学们可以根据自己的需求来修改模板的样式。
怎么用浏览器打开？
在linux终端项目目录输入命令`firefox index.html`
也可以进入文件夹，在图形化界面中右键选择用浏览器打开

有的同学可能已经发现这份模板是可编辑的了。

所有的文字栏目都是可以随意编辑的：

![1-2.2-1](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472193804571.png)

点击图片可以通过图片的url地址替换：

![1-2.2-2](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472193923263.png)

替换后：

![1-2.2-3](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472193962395.png)

微信号不想留就把整个栏目删掉：

![1-2.2-4](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472194078738.png)

可增加新的条目：

![1-2.2-5](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472194174398.png)

可通过点击编辑进度条：

![1-2.2-6](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472194207572.png)

注意敏感信息不要发布在页面上，我们仅在需要打印简历时用到这些信息：

![1-2.2-7](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472195222729.png)

注意简历的内容不要超出背景的高度。

编辑完自己的简历以后，就把修改后的代码复制下来，替换掉原`index.html`里的代码。

Firefox 下：

打开查看器：

![1-2.2-8](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472743591122.png)

复制 html 标签的外部 HTML：

![1-2.2-9](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472743628808.png)

然后将`index.html`中的 html 标签的所有内容（包括 html 标签）替换掉即可。

我的本意是发布后的简历页面仍是可编辑的，这方便我日后直接在上面编辑手机等个人信息后保存打印。

不喜欢这样子的同学可以修改`static/js`下的`script.js`文件，操作非常简单，删除该文件下的所有内容，然后加上下面这一句。

```
$(document).ready(function($){
    $("*").removeAttr('contenteditable');        
})
```

这一句是为了去掉页面上所有元素的可编辑属性。

最后可以在 CSS 文件内再改改样式。

#### 2.3部署简历文件

首先需要每位同学都有自己的 Github 账号：https://github.com/

没有就快去注册一个吧。然后新建一个仓库，名字取 `cv` 或是 `resume` 皆可，之后先别跟着它给的步骤做。

Github Pages 支持托管的页面分两类，个人/组织页面 与 项目页面，其主要区别就是托管位置的区别。如下表所示（这里略去组织，它跟个人是差不多的）：

| 类型     | 页面域名 & 托管位置              | 页面源文件所在的分支                               |
| -------- | -------------------------------- | -------------------------------------------------- |
| 个人主页 | `username.github.io`             | `master`                                           |
| 项目主页 | `username.github.io/projectname` | `master`、`gh-pages` 、或是在`master`的`doc`目录下 |

如果想使用个人主页，那么就创建一个名为 `username.github.io` （username需要替换为你的用户名）的库，在主分支`master`上托管你的页面代码。

如果是使用项目主页，那么可以选择将代码托管在`master`、`gh-pages`、或者`master`的`doc`目录下，其中`gh-pages`是默认的页面托管分支，如果想使用`master`，可在项目页面的设置栏中进行切换。

![1-2.3-1](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472195842571.png)

![1-2.3-2](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472195863850.png)

（由于现在仓库还是空的，上面的选项还无法选）

无论使用哪一种页面操作都是差不多的，这里就用项目页面来做演示了，由于我们所有的代码就只有页面代码而已，那么就直接在`master`分支上进行托管吧。

先在本地仓库做一次代码提交：

```
$ git add .
$ git commit -m 'commit my cv'
```

在项目页面找到你的仓库地址后输入：

```
$ git remote add origin 你的远程仓库地址
$ git push -u origin master
```

代码提交到远程仓库后，在项目页面设置 Github Pages 使用的托管源。

![1-2.3-3](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472195989896.png)

现在你可以访问`https://你的用户名.github.io/resume/`这个地址了，恭喜，简历页面已成功部署在了 Github Pages 上。

#### 2.4保存简历为pdf格式

笔者考察过多个在线转换 pdf 的网站以及 js 保存 pdf 的方案，效果都不甚理想。最后发现这一步其实可以很简单，你只要打开浏览器的打印选项然后它其实是可以直接帮你保存为 pdf 的！这里还是推荐使用 Chrome ，Firefox 似乎无法删页脚与页眉。

Firefox 下：

![1-2.4-1](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472196138730.png)

勾上打印背景图像与颜色，页脚和页眉都设置成空白

![1-2.4-2](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472196192024.png)

![1-2.4-3](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472196161231.png)

Chrome 下：

![1-2.4-4](https://doc.shiyanlou.com/document-uid8834labid2084timestamp1472196586710.png)

因为等到打印 pdf 的时候，那个页边距是可以再调的，所以笔者比较倾向于在保存的时候不保留页边距。

### 三、总结

本课程主要是给没有接触过 Github Pages 的同学演示一遍它的基本使用，关于其它主题如自定义域名，自定义 404 页面等可在 [Customizing GitHub Pages](https://help.github.com/categories/customizing-github-pages/) 中找到参考。这里还需要再三提醒一句，千万不要在发布的简历中加上个人身份敏感信息呀！最后再给看到这里的同学一个福利吧：https://www.canva.com/templates/resumes/

### 四、参考资料

- [GitHub Pages Basics](https://help.github.com/categories/github-pages-basics/)
- [Customizing GitHub Pages](https://help.github.com/categories/customizing-github-pages/)
- [HTML5 Editable Table](http://codepen.io/ashblue/pen/mCtuA)
- [一看 star 数就知道一定是最牛的简历](https://github.com/DIYgod/Resume)
- [freepik 上的好看简历](http://www.freepik.com/free-psd/editable-cv-format-download_716578.htm)

