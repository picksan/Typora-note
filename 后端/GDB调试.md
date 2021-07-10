# GDB调试

## 编译时需加入调试信息

```
gcc -g -Wall program.c -o program
```

## 调试程序

```
#调试程序
gdb 程序名字
#第一行开始
start
#运行到断点
run
#显示所有断点的信息
i b
#第10行打断点
b 10
#显示test.c 的main函数
l test.c:main
#单步，会进入函数体
s/step
#单步，不进入函数体
n/next
#打印i的值
p i
#删除断点
d
#跳出函数体
finish
```

## 设置core文件大小

core文件在程序运行出错时会产生，可以用gdb查看具体出错情况。core文件默认为0，需要自己设置。

设置core文件大小为无限大

```
ulimit -c unlimited
```

