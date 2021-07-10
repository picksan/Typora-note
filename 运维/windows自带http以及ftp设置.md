# 搭建网络服务器

参考链接：[Windows下搭建FTP服务器 - 子非魚！ - 博客园 (cnblogs.com)](https://www.cnblogs.com/zhangfengfly/p/6879513.html#:~:text=为windows开启ftp功能：控制面板-->程序和功能-->打开或关闭Windows功能将如图的选框选中. 2、添加FTP站点：打开控制面板-->管理工具-->双击Internet信息服务（IIS）管理器如下图添加FTP站点. 3、设置站点名称和想要公开的路径. 4、绑定IP地址和ssl设置：.,IP地址填本机地址，端口默认21，ssl是一种数字加密证书，可申请，在此没有可选择无。. 5 、 设置权限，建议设置成读取状态，点击完成就大功告成了。. 6、如何登陆测试.)

- http服务器

- ftp服务器

## 搭建web服务器

1.为windows开启http功能：控制面板->程序和功能->启用或关闭windows功能

![image-20210529171931672](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185944.png)

2.勾选Internet information services（IIS）的ftp服务器（可选），web管理工具，万维网服务

![image-20210529172123035](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185949.png)

3.安装完后，需要配置，进入

重新进去控制面板，找到管理工具

![image-20210529172327158](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710190402.png)

点击IIS管理器

![image-20210529172405612](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185953.png)

根据需要配置

![image-20210529172447963](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185958.png)

ftp站点需要自己添加，右击网站添加ftp站点

图略

