# Makefile编写

最终版makefile

```
# 使用函数 查找当前目录下 所有.c文件
src=$(wildcard ./*.c)
# 使用函数 替换字符串，把所有.c文件 替换为 对于的.o文件
objs=$(patsubst %.c,%.o,$(src))

target=app

$(target):$(objs)
    $(CC) $(objs) -o $(target)

%.o:%.c
    $(CC) -c $< -o $@

# 伪文件 clean 不要和当前路径下clean比较时间
.PHONY:clean
clean:
    rm $(objs) -f
    #rm -f 无需确认,删除
```

第一版

```
#目标...:依赖...
#   命令(shell 命令)前面的空格必须是tab键不能是空格
#   ...

test:test.c
    gcc test.c -o test
```

第二版

```
test:test.o
    gcc test.o -o test

test.o:test.c
    gcc -c test.c -o test.o

.PHONY : clean
clean :
    -rm test test.o
```

第三版

```
# 第三版
target=test
src=test.o
CC=gcc
# 程序依赖 .o文件
$(target):$(src)
    $(CC)  $^ -o $@

# .o文件依赖.c文件
%.o:%.c
    $(CC) -c $< -o $@

# 清理.o文件
clean :
    -rm *.o
```

最终版

```
# 使用函数 查找当前目录下 所有.c文件
src=$(wildcard ./*.c)
# 使用函数 替换字符串，把所有.c文件 替换为 对于的.o文件
objs=$(patsubst %.c,%.o,$(src))

target=app

$(target):$(objs)
    $(CC) $(objs) -o $(target)

%.o:%.c
    $(CC) -c $< -o $@

# 伪文件 clean 不要和当前路径下clean比较时间
.PHONY:clean
clean:
    rm $(objs) -f
    #rm -f 无需确认,删除
```

![20210710132636](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710182919.png)

```
#2021/3/30
#目标文件
target=app
#源文件 .cpp文件
src=myth01.cpp
#目标文件 .o文件
obj=$(patsubst %.cpp,%.o,$(src))
#库文件
lib= -pthread
#编译器和选项 -Wall生成所有警告信息 -std=c++11使用c++11标准
CXX=g++
flags= -Wall -std=c++11

#链接为可执行文件
$(target):$(obj)
    $(CXX) $^ -o $@  $(lib)

#编译规则 $@代表目标文件 $< 代表第一个依赖文件
%.o:%.c
    $(CXX) -c $< -o $@  $(flags)

#清除 rm -I显示提示 -r递归删除 -v显示删除情况 -f强制执行忽略错误
.PHONY:clean
clean:
    @echo 开始清理
    rm -I -r -v $(target) $(obj)
```

