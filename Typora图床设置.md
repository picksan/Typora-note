# Typora图床设置

1. github设置

   - github创建新仓库并初始化

   - github创建令牌，并记录下来。忘记了就删掉原来的，重新创建。只会出现一次，妥善保管。

2. Picgo（app)

   - 下载软件[PicGo](https://picgo.github.io/PicGo-Doc/)

   - picgo开启时间戳重命名

   - 配置github图床
   
     配置其他图床比如gitee时，需要安装插件。而插件的安装需要nodejs环境。
   
     [Node.js (nodejs.org)](https://nodejs.org/en/)
   
   - 设置自定义域名
   
     使用cdn加速，[jsDelivr - A free, fast, and reliable CDN for open source](https://www.jsdelivr.com/?docs=gh)
   
     原因：github图片链接的域名被dns污染了，找不到正确的服务器地址
   
     使用方法：
   
     在自定义域名中设置就ok了
   
     ```
     https://cdn.jsdelivr.net/gh/你的github用户名/github仓库/
     ```
   
     

![image-20210710184228232](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185600.png)

3. Typora偏好设置

   - 配置本地存储图片的位置

     勾选相对位置

     修改复制图片复制到的文件夹

   - 配置上传服务

     Picgo（app）

     配置Picgo路径

     验证图片上传

![image-20210710183414371](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185607.png)

参考链接

[Typora使用技巧之插入图片及图片上传 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/344941041)

[Typora+Picgo+Gitee构建网络图床笔记 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/178938338)

[PicGo图床与Typora（PicGo+Typora+GitHub的完整设置） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/168729465)

[Typora的图片利用PicGo上传到Github图床 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/341875598)

