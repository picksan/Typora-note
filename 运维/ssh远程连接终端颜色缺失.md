ssh远程连接终端颜色缺失

终端颜色缺失

原因：用户目录下**.bashrc**文件缺失

解决方法：

```shell
//locate命令列举所有 .bashrc文件
locate .bashrc
//复制其中一个到 用户目录
cp  /etc/skel/.bashrc   ~/
//启用配置
cd ~
source .bashrc
```

使用私钥登录服务器

```
ssh -i ~/.ssh/TenXun/id_rsa.rsa ubuntu@42.192.228.246
```



| -i 私钥路径 | ~代表家目录，windows平台是C:\Users\10341，linux平台是/home/pi |
| ----------- | ------------------------------------------------------------ |
|             |                                                              |

对应公钥需要在服务器记录 **~/.ssh/authorized_keys**

