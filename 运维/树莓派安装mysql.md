# 树莓派安装mysql

树莓派安装mysql会提示安装mariadb代替

```shell
# 安装mariadb服务端和客户端
sudo apt-get install mariadb-server mariadb-client 
# 安装mariadb客户端编程开发库
sudo apt-get install libmariadbclient-dev
```

安装完进入

```shell
sudo mysql -uroot -p
```

 登录 如果你安装过程中没有出现让你设置密码的话，那你就输入 sudo mysql -uroot 回车登录，初始密码一般为空。

## mysql安装完，普通权限无法访问

问题原因：root默认使用了无密码的插件登录，需要改成密码登录

[(4条消息) 服务器使用mysql -u root -p报错解决_wavehaha的博客-CSDN博客](https://blog.csdn.net/wavehaha/article/details/114703149)

```mysql
SELECT user,authentication_string,plugin,host FROM mysql.user;
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';

use mysql;  
update mysql.user set authentication_string=password('123456') where user='root' and Host ='localhost';
update user set plugin="mysql_native_password"; 
flush privileges;  
quit;
```

试验成功的方法

```mysql
root@ubuntu:mysql mariadb>use mysql;
mariadb>update user set password=PASSWORD("123456") where User='root';
mariadb>update user set plugin="mysql_native_password"; 
mariadb>flush privileges;
mariadb>exit;
```

[(4条消息) 解决mariadb设置初始密码不生效方法_通往未来的路-CSDN博客](https://blog.csdn.net/qq_38375945/article/details/117253660)

```shell
启动mysql 
sudo service mysql start 
启动mariadb（猜测） 
sudo service mariadb start
```

