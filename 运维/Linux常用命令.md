## Linux常用命令

```
ping raspberrypi.local
# ping树莓派
sudo shutdown -h now
# 关机
ps -ef|grep ./server
# 查看名叫server的相关进程
ps -T -p 120
# 查看进程号120底下的所有线程
ctrl+z
暂时停止进程
fg
恢复停止的进程
ctrl+c
终止结束进程
 ulimit -n
 查看和修改文件描述符数量限制
```