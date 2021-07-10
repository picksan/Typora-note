编译静态库

```
#编程成.o
gcc -c *.c
#打包成静态库
ar rcs libcalc.a add.o div.o mult.o sub.o
#打包程序 -I 头文件路径 -l 静态库名字 -L 静态库路径
gcc main.c -o app -I ../include/ -l calc  -L ../lib/
```

编译动态库

```
#编译成 .o
gcc -c -fpic a.c b.c
#编译成 动态库
gcc -shared a.o b.o -o libcalc.so
#编译成可执行文件
gcc main.c -o app -I ../include/ -l calc  -L ../lib/
#查看 可执行文件里的动态库的地址
ldd app
#临时增加环境变量
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/sdm/Linux/library/lib
#永久增加需要写入 用户环境变量
cd ~
vim ./bashrc
在最后一行增加，按i插入
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/sdm/Linux/library/lib
esc退出编辑模式
:wq 保存退出
```

