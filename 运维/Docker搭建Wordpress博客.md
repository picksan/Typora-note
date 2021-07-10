# Docker搭建Wordpress博客

```
# 激活docker用户组的修改
newgrp docker 

# 镜像
## 列出存在的镜像
docker image ls
## 拉取wordpress镜像
docker pull wordpress
## 拉取mysql5.6版本的镜像
docker pull mysql:5.6

# 容器操作
docker container ls
docker container start
docker container stop
docker container prune

# 启动 mysql容器
docker run -d \
    --name wordpress-mysql \
    -e MYSQL_ROOT_PASSWORD=123456 \
    -p 3306:3306 \
    mysql:5.6
    
# 启动 wordpress容器并连接mysql容器
docker run -d \
 --name wordpress-wordpress \
 --link wordpress-mysql:mysql \
 -p 8080:80 \
 wordpress
 
# 运行的容器进程
docker ps 
docker ps -a

# 进入容器
docker exec  -it mysql bash
mysql -uroot -p123456
```

**注意：对于 wordpress容器来说 mysql数据库是 mysql:3306而不是localhost**

run过一次后，只要docker container 某某指令就行

```
 # 查看当前运行的容器实例
 docker container ls
 # 停止某个容器
 docker container stop xxxid
 # 查看所有停止的容器
 docker container ls -a
 # 启动某个停止的容器 
 docker container start xxxid
 # 重启某个容器
 docker container restart xxxid
```

wordpress~~账号：sdm 密码：sdm17061221 数据库账号：root 密码：123456~~

wordpress中关于数据库的设置在配置文件wp-config.php

## 修改容器内文件（踩坑）

由于容器内缺少编辑器，使用复制文件到外面修改，再复制回容器的方法

容器内路径/var/www/html/wp-config.php

```
# 查看容器编号
docker container ls
# acaf就是wordpress的容器编号，复制容器内文件到外面自己用户目录下
docker cp acaf:/var/www/html/wp-config.php /home/ubuntu/mycode/wp-config.php
# 修改自己的文件
。。。。。。。。。
# 将修改完的文件，复制回去
docker cp  /home/ubuntu/mycode/wp-config.php acaf:/var/www/html/wp-config.php
```

参考：

[使用Docker搭建MySQL服务 - sablier - 博客园 (cnblogs.com)](https://www.cnblogs.com/sablier/p/11605606.html)