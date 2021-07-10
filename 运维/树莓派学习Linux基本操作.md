> 参考书籍：树莓派开始，玩转Linux（博文视点出品）
>
> 可以在微信读书中找到电子版



# **软件更新以及配置**

更新软件源

`sudo apt-get update`

升级已安装的程序

`sudo apt-get upgrade`

安装软件，例如Mysql

`sudo apt-get install mysql`

删除软件

`sudo apt-get remove mysql`

以上apt-get remove不会删除配置文件，更彻底的删除

`sudo apt-get purge mysql`

## 修改软件源服务器，一般国外的官方的软件源下载特别慢，修改为国内镜像

所有树莓派的软件源都可以在这里找到：https://www.raspbian.org/RaspbianMirrors

1.终端中输入`sudo nano /etc/apt/sources.list`

sudo 是指用系统管理员权限启动，nano 是树莓派内置的轻量文本编辑器，而 /etc/apt/sources.list 就是软件源的配置文件地址了。

2.编辑这个文件，把原来的注释掉（最前面加#号），在文件最顶部添加下面的内容：

```
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib 
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib
```

3.按ctrl-o保存，按ctrl-x关闭

4.同样的方法，把/etc/apt/sources.list.d/raspi.list 文件也替换成下面的内容：

`deb http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui`

## **远程连接**

前提：

开启树莓派的ssh服务器

sudo raspi-config

找到Interfacing Options，进入找到SSH，enable开启

1.局域网ssh远程连接

mac os 或者 linux系统直接在终端中输入

ssh pi@树莓派的ip地址

windows使用终端工具putty，xshell6等，ssh连接，输入相应的ip地址，用户名和密码

使用ssh客户端时，除了说明ip地址，还需要说明端口号。忽略端口号时，ssh客户端默认为端口22

如何查看树莓派ip地址

- 在树莓派中查看ip地址

ifconfig

- 在同一局域网UNIX系统中查找树莓派的地址

arp

- 下载手机app等，查看同一局域网的设备的ip地址
- windows中查看树莓派的IP地址

ping raspberrypi.local

如果环境下有多个树莓派，需要给树莓派重新命名，否则为raspberrypi

raspberrypi-2

raspberrypi-3

2.互联网ssh连接

1. NAT端口映射，一组公网ip和端口号能对应唯一的私网ip和端口号

2.反向ssh隧道，需要借助一个服务器

**文件**

1.硬链接 

会破坏文件树状结构，不推荐，修改一个其中一个文件会导致所有文件同步修改，要删除所有的硬链接，才能删除该文件

建立硬连接

ln file.txt /home/pi/movies/another_file.txt

删除硬连接

unlink another_file.txt

2.软链接

相当于windows的快捷方式，本质是个文件，文件类型symbolic link，在文件中包含链接指向的文件的决定路径。当读写文件时，会把读写操作导向软链接所指向的文件，linux的软链接实际上就是linux的"快捷方式"

创建软链接

ln命令加上 -s，来创建软链接

ln -s file.txt /home/pi/file-link.txt

**/home/pi/file-link.txt** 是一个软链接文件

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185748.png)

3.新建空文件

touch empty.txt

4.新建一个目录

mkdir good

5.删除一个空目录

rmdir good

6.复制

cp file.txt file_copy.txt

7.删除

rm file.txt

8.复制某个目录开始的子文件树

cp -r doc doc_copy

9.删除整个目录

rm -r doc_copy

10.移动文件

mv file.txt /home/pi/file1.txt

11.查看文件内容

cat file.txt

12.编辑文件比如nano vi vim

nano file.txt vi file.txt vim file.txt

13.使用shell的echo命令向文件写入字符

echo '123' > file.txt

14.查看当前目录的文件

ls

15.查看当前路径

pwd

16.切换目录

cd 绝对路径 cd /home/pi 切换到家目录，就是当前登录的账户的名字的目录，例如pi cd ~ cd 相对路径 .当前路径 ..当前路径的上级路径（父目录） cd ./code 根目录/ /是正斜杠 \是反斜杠，记忆方法，八这个字的左撇和右撇 windows里目录是反斜杠\,linux是正斜杠/ linux系统从根目录/开始

\17. 自动填充

键盘上的TAB键可以帮我们在shell中输命令时，自动补充命令的名字，或者文件的名字，

当当前目录下有多个前缀相同的文件，按两下tab可以显示，所有可能填充的值

18.清空shell屏幕

clear

**文件搜索**

1.find命令

find path ... [expression]

path是需要搜索的路径 可以有多个，expression是可选的表达式，说明对目标文件进行的操作。表达式由主操作（Primary）和运算（Operand）组成

例子：

打印硬盘中所有文件后缀为c的文件

find / -name "*.c"

打印当前目录所有后缀名不是.c的文件

find . -not -name "*.c"

输出当前目录所有后缀名为.c文件的详细信息

find . -name ".c" -ls

2.locate

查找名为grep的文件

locate grep

忽略大小写，查找以l开头t结尾的文件

locate -i l*t

locate命令不是实时的，find是实时的，locate是从一个数据库中查找文件

更新locate查找的数据库

sudo updatedb

sudo 是增加管理员（root）权限的意思，一般还会要求你输入管理员（root）密码

**从程序到进程**

**在终端中，创建一个c文件到编译执行**

1.创建c文件

nano demo.c

备注，nano 会直接创建文件（原本不存在），或者修改该文件（已存在），是一个编辑器，nano是个自带的程序

demo.c的文件内容

\#include <stdio.h> int main(void){    int a;    int b;    int c;    a=1;    b=2;    c=a+b;    printf("sum is %d",c);    return 0; }

根据nano下面的提示ctrl+o保存，按回车确定保存的文件名

按ctrl+x退出nano

2.编译

linux环境下常用gcc编译器

gcc demo.c -o demo

不指定 编译后的名字，会自动生成 a.out文件

3.运行

./a.out

终端中就会显示c程序运行的结果

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185755.png)

nano界面

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185759.png)

停止某个正在运行的程序，比如下载，安装某程序时，太慢

ctrl+z

一般linux发行版有已经编译好的二进制可执行文件

可以通过apt-get 程序名下载，看平台是否支持该命令debian系的

centos 是yum ...

**编译开源软件，从源代码开始编译**

按照惯例，一般会有一个名为configure的脚本用于设置

编译第一步，就是运行该脚本,根据提示进行设置

./configue

随后，需要运行make命令

make

一般c项目会有错综复杂的头文件以及依赖关系，

使用makefile文件可以避免在命令行中手打一长串命令，

编辑好makefile以后，只需要make一下，就会自己编译

不过makefile书写繁琐复杂，有一种更方便的方法就是使用cmake生成makefile

cmake使用比直接书写makefile简单

最后，把编译好的二进制文件放到configue设定的目标路径中

sudo make install

**进程**

应用程序不等于进程（Process）

进程是程序的一个具体实现，就是执行时的实体

1.查询正在运行的进程

ps -eo pid,cmd

ps命令 -e表示列出全部进程，-eo pid,cmd 选项表示我们需要的信息

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185807.png)

返回结果分两列，每一行代表一个进程

第一列PID是进程id，每个进程都要一个唯一的PID来代表自己

无论内核还是其他进程都是根据PID来识别出该进程的

第二列CMD是进程所对应的程序，以及运行时传递给程序的参数

第二列中有一些由中括号括起来的，是内核的一部分功能，被打扮成进程的样子，方便操作系统管理

此外PID为1的进程一定是由/sbin/init程序运行形成的，当Linux启动时，init是系统创建的第一个进程，这个进程会一直存在，直到关闭计算机。其他进程都是常见的应用进程，比如cron ps

同一个程序可以执行多次，从而产生多个进程。即使一个程序的进程还未完成，我们还是可以用同一个程序运行出更多的进程

操作系统的一个重要功能就是管理进程，为进程提供必需的计算机资源，例如给进程分配内存空间

**万物皆是文本流**

数据是计算机最宝贵的财富。在Linux中，文本流（Text Stream）是不同程序、不同文件之间的数据桥梁。通过这一数据桥梁，linux的不同模块之间可以方便地进行协助。

在计算机系统中，除了存储设备，还有很多其他设备有读写数据的需求。GPIO和UART端口都要读写功能。Linux把所有读写数据的对象都当作文件。在Linux中，我们操作设备文件就可以和设备进行数据交流。在UART编程中，我们可以通过/dev/AMA0这一文件和UART端口的设备直接对话。在/dev目录下，还可以找到很多其他的设备文件

"Everything is a stream of bytes" 万物皆是文本流

文本流具有以下特征：

文本性：数据以字节为单位，可以转换成文本

有序性：数据的前后顺序不会错乱

完整性：数据的内容不会丢失

**标准输入，标准输出，标准错误**

文本流存在于linux的每个进程中。当Linux启动一个进程时，会自动打开三个流的端口：标准输入（Standard Input）、标准输出（Standard Output）、标准错误（Standard Error），这三个端口类似于入口，出口，紧急出口，进程经常会通过这三个端口进行输入输出。虽然一个进程总会打开这三个流，但进程会根据需要有选择地使用

以bash进程为例，bash的标准输入连接到键盘，标准输出和标准错误连接到屏幕。

**重新定向**

当bash执行一个命令时，这个bash会创建一个子进程用于命令的执行。默认情况下，由于子进程的标准输出与bash相同，因此输出内容出现在bash窗口。如果想让文本流流到文件，而不是显示在屏幕，我们可以利用重新导向的机制。比如将ls命令输出的文本流导向一个文件

ls > output.log

这里的>符号重新定向了ls的标准输出。标准输出的文本流不再出现在bash窗口中，而是有序的地存储于目标文件output.log中。计算机会新建一个output.log文件，并将命令行的标准输出指向这个文件。在这个过程中，文本流就像火车换轨，走向不同的方向。

另一个符号>>也可以重定向

ls >> a.txt

\>和>>的区别，如果a.txt不存在，那么>>和>符号相同，都是新建a.txt文件，并把文本流导入。如果a.txt已经存在，ls产生的文本流会附加在a.txt的结尾，而不是像>那样每次新建a.txt

单一的>和>>只会重新定向标准输出。如果标准错误有端口输出，那么输出的内容依然按照默认情况，输出到bash窗口

如果想重定义标准错误，可以使用2>

删除一个不存在的文件 rm non-file 2> error.log

可以分别把标准输出和标准错误重新定向到不同的目的地

ls 1> output.log 2> error.log

1代表标准输出

还可以用&>同时将标准输出和标准错误指向同一文件

ls &> output_error.log

grep命令

grep命令检查一段文本流中是否包含有特定的文本。

grep abc

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185815.png)

可以重定向grep的标准输入，让输入内容来自文件而不是键盘

grep abc < content.txt

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185822.png)

在重新定向标准输入的同时，可以重新定向标准输出

grep abc < content.txt &> output.txt

**管道**

重新定向是把一个进程的标准输出写入文件。管道（pipe）也是变更文本流的方向。不过管道的目的地是另一个进程。借用管道，我们可以把一个进程的输出变成另一个进程的输入。这样我们可以用管道把两个或者多个命令连接在一起，从而让它们像流水线一样工作，不断地处理文本流。在bash中，用 | 表示管道

echo Hello | grep lo

命令echo的功能是把作为参数的文本输出到标准输出。管道把echo输出导入grep命令。这里的grep命令是从文本流中寻找"lo"字符串。由于输入的"Hello"中包含"lo"，所以grep命令会打印出"Hello"

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185827.png)

linux的各个命令实际上高度专业化，并相互独立，每个命令都只关注一个小的功能。但通过管道，我们可以把这些功能合在一起，实现一些复杂的目的。比如，我们想从一个文件中找出所有包含Tom的行，并按照字母表排序。文件input.txt内容如下：

2017 Tom 2018 Nico 2019 Tom

想要实现上面功能，只需简单的一行

grep Tom < input.txt | sort

统计当前目录中名字包含了txt的文件的总数

ls | grep txt | wc -l

**文本相关命令**

long_night.txt

It`s a long night. I am far from home. Home, Home, My sweet home.

输出整个文件，可以用cat

cat long_night.txt

命令head和tail分别从文件开头和结尾输出。

输出开头三行

head -3 long_night.txt

输出末尾两行

tail -2 long_night.txt

diff命令，输出两个文件不同的部分

diff file1 file2

上述文件输出命令，以及输出参数的echo命令，经常作为文件流的起点。有了文件流就可以用管道连接起多个命令，从而对文件内容进行编辑

cat long_night.txt | sort | uniq > another_night.txt

排列 long_night.txt的内容，删除重复行，输出到another_night.txt

**用户管理**

找出自己的身份

who am i

返回所有的登录的用户

who

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185833.png)

在Linux中可以用文本的用户名来指代一个用户。比如用write命令可以用来给同一Linux下的其他用户发信息。用户anna发信息给lvor

echo "where is your draft?" | write lvorec

用户不仅是一个单一个体，同时还是一个用户组（group）的成员。组是多个用户的集合，组内用户享有某些共同的权限。一个用户至少属于一个组。

查看用户所在组

groups

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185840.png)

返回用户anna所在组

groups anna

用户可以通过文本形式的用户名记住每个用户。在机器底层，会用一个数字代表用户身份。用户个体可以用UID表示。组可以用GID表示

id pi

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185844.png)

**root用户和用户创建**

在linux系统中有一个特殊的root用户，拥有最高的权限。

使用su命令可以切换root用户

root用户容易误操作，为避免类似的灾难，linux引入了sudo

如果普通用户有权使用sudo，那么他可以使用sudo来以root身份执行命令。由于sudo是临时地扩张用户权限，误操作的概率会大大降低

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185848.png)

创建新用户

sudo adduser tommy

切换用户tommy

su tommy

删除用户tommy

sudo deluser --remove-home tommy

创建用户组

sudo groupadd genius

删除用户组

sudo groupdel genius

Linux的用户信息保存在/etc/passwd中，通过这个文件，可以对操作系统的用户和组进行总览。

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185853.png)

每一行的含义

用户名：密码：UID：GID：描述：用户目录：登录shell

密码部分用x表示，这个符号含义是密码以密文的方式，保存在/etc/shadow

大部分用户会有一个用户目录，用于存放自己的文件，登录时，bash的工作目录，通常设置成该用户目录。用户root的用户目录在/root下，而普通用户的目录都位于/home下

最后的/bin/bash说明了登录之后默认使用的shell，/bin/bash是bash的程序文件

有些行的记录使用的shell是nologin或者false，nologin会拒绝登录，命令false则什么都不做。因此这些用户没法像普通用户一样，通过shell来操纵操作系统，因此被称为伪用户。

为了系统管理方便，操作系统创建了这些伪用户。很多程序会以伪用户的身份运行，以便享有对应的权限。以用户mail为例：

mail:x:8:8:mail:/var/mail:/usr/sbin/nologin

当操作系统调用电子邮件相关程序时，会用到该伪用户

同样，用户组的信息被保存在/etc/group中

组名：组密码：GID：用户列表

文件的权限

ls -l file.txt

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185900.png)

返回部分可以分为5个部分

-rw-r--r-- 1 pi pi 108 2月  20 16:18 demo.c

1. -rw-r--r-- 文件的类型和权限
2. 1 文件的链接数
3. pi pi 文件的拥有者和拥有组
4. 108 文件的大小，单位是字节
5. 2月  20 16:18 上一次修改文件的时间

 rw-r--r--分为三部分rw-，r--，r--，分别对应拥有者，拥有组，其他人

r读w写x执行

![img](https://cdn.jsdelivr.net/gh/picksan/picgo//pic/20210710185904.png)

文件权限管理

chown修改文件的用户，以及用户组

sudo chown anna:anna file.txt

chmod改变文件的权限标志

文件的所有者使用

chmod 755 file.txt

其他用户必须有管理员权限

sudo chmod 755 file.txt

755三个数字对应三类用户权限，第一个所有者，第二个所有组，第三个其他用户

7用2进制表示为111，分别代表rwx权限

5用二进制表示101，代表r-x

目录也是文件，可以有用户权限，没有权限的目录将无法访问以及删除修改文件

提交到后台使用 nohup