# C 语言版 flappy_bird

### 一、实验介绍

#### 1.1 实验内容

Flappy bird 想必是一款大家都不陌生的游戏，当初因为几近自虐的超高难度反而使它红极一时。今天我们的课程就是要通过 C 语言来实现一款属于自己的 Flappy bird 。

#### 1.2 实验知识点

- 绘图库 `ncurses` 的使用
- C 语言逻辑控制

#### 1.3 实验环境

1. Xfce 终端（Xfce Terminal）：Linux 命令行终端，打开后进入 Bash 环境，可以执行 Linux 命令。
2. GVim：非常好用的编辑器，最简单的用法可以参考课程[《Vim 编辑器》](https://www.lanqiao.cn/courses/2)。

#### 1.4 适合人群

本课程适合有 C 语言基础，想做练手项目的同学，可以有效的学习 `ncurses` 绘图库的使用，做一些有趣的事情。

#### 1.5 代码获取

在 linux 终端运行以下代码以下载源代码到实验环境：

```
wget https://labfile.oss.aliyuncs.com/courses/146/my_flappy_bird.c
```

#### 1.6 最终效果

最终我们的游戏做出来效果是这样的：

![1.6-1](https://doc.shiyanlou.com/document-uid18510labid438timestamp1526550402500.png)

其中，为了降低实现的难度，我们用单个字符 `O` 来表示 bird ，用符号 `*` 组成的矩阵表示障碍物。

### 二、实验原理

#### 2.1 基础知识

我们的项目用到了一点数据结构的知识，还涉及到了 Linux 的一些系统调用。

此外，我们还用到了一个文本界面的屏幕绘图库 `ncurses` 。所以在最后编译时需要加上 `-lcurses` 选项。

#### 2.2 设计思路

要实现字符界面版的 Flappy bird ，我们从以下三个关键点的实现入手：

1. 程序要能响应键盘事件；
2. 字符界面要能实时刷新；
3. 使 bird 给人一种视觉上在往前飞行的感觉。

对于以上三个问题我们的解决方案如下：

1. 使用 Linux 提供的系统接口获取键盘事件；

2. 使用 ncurses 库函数绘制字符界面；

3. 要使 bird 有往前飞行的感觉： 最直接的一个思路是让 bird 在水平方向上从左往右移动，但是这样会使得 bird 在某一时刻超出 右边界；

   我们不妨反向思考，人在前进的汽车上看到窗外的风景是倒退的（运动是相对的），所以我们让 障碍物从右往左移动，这样既能得到的一样的视觉效果，也避免了 bird 超出边界的问题。

### 三、实验步骤

这里我们将按照实际操作的顺序给出在 Linux 环境中的代码和重要环节的实际截图。代码分为两部分，一是在 Linux 下的操作，二是在 GVim 编辑器中编写 C 代码。

#### 3.1 定义常量

- 首先打开 Xfce 终端，执行以下命令安装 `ncurses` 库：

```
sudo apt-get install libncurses5-dev
```

- 进入目录 `Code` ，创建项目文件 `my_flappy_bird.c` ，打开 Gvim 编辑器：

```
cd Code
touch my_flappy_bird.c
vim my_flappy_bird.c
```

![3.1-1](https://doc.shiyanlou.com/document-uid18510labid438timestamp1526550450648.png)

- 接下来是编写 C 代码，第一步是头文件：

```c
# include <curses.h>
# include <stdlib.h>
# include <signal.h>
# include <sys/time.h>
```

- 在写 `main()` 函数之前让我们先来完成一些基础工作，因为我们是终端字符界面，一切离不开 ASCII 字符，因此我们需要定义一些常量。

  我们用 `*` 来表示背景里的柱子，用 `O` 来表示 bird。代码如下：

```c
# define CHAR_BIRD 'O'  // 定义 bird 字符
# define CHAR_STONE '*'  // 定义组成柱子的石头
# define CHAR_BLANK ' '  // 定义空字符
```

 背景中的柱子用单向链表存储，结构体定义为如下：

```c
typedef struct node {
    int x, y;
    struct node *next;
}node, *Node;
```

 再定义几个全局变量：

```
Node head, tail;
int bird_x, bird_y;
int ticker;
```

 提前声明一下我们将要创建的函数：

```
void init();  // 初始化函数，统筹游戏各项的初始化工作
void init_bird();  // 初始化 bird 位置坐标
void init_draw();  // 初始化背景
void init_head();  // 初始化存放柱子的链表的链表头
void init_wall();  // 初始化存放柱子的链表
void drop(int sig);  // 信号接收函数，用来接收到系统信号，从右向左移动柱子
int set_ticker(int n_msec);  // 设置内核的定时周期
```

#### 3.2 定时问题

现在我们来解决如何让背景定时移动的问题。这里要用到 Linux 系统提供的功能，即信号。

不知道什么是信号？没关系，可以理解为 Linux 内核有个定时器，它每隔一段时间就会向我们的程序发送一个信号，我们的信号接收函数 `drop(int sig)` 就会被自动执行，我们只要在 `drop(int sig)` 里移动柱子就行了。同时，由于信号是 Linux 内核发送的，所以不存在因为接收信号而导致我们的键盘信号接收产生阻塞的情形。

- 下面就来实现我们的代码，设定内核的定时周期的 `set_ticker(int n_msec)` 函数：

```c
int set_ticker(int n_msec)
{
    struct itimerval timeset;
    long n_sec, n_usec;

    n_sec = n_msec / 1000;
    n_usec = (n_msec % 1000) * 1000L;

    timeset.it_interval.tv_sec = n_sec;
    timeset.it_interval.tv_usec = n_usec;

    timeset.it_value.tv_sec = n_sec;
    timeset.it_value.tv_usec = n_usec;

    return setitimer(ITIMER_REAL, &timeset, NULL);
}
```

- 信号接收函数 `drop(int sig)` :

```c
void drop(int sig)
{
    int j;
    Node tmp, p;

    // 将原先 bird 位置的符号清除
    move(bird_y, bird_x);
    addch(CHAR_BLANK);
    refresh();

    // 更新 bird 的位置并刷新屏幕
    bird_y++;
    move(bird_y, bird_x);
    addch(CHAR_BIRD);
    refresh();

    // 如果撞上柱子则结束游戏
    if((char)inch() == CHAR_STONE)
    {
        set_ticker(0);
        sleep(1);
        endwin();
        exit(0);
    }

    // 检测第一块墙是否超出边界
    p = head->next;
    if(p->x < 0)
    {
        head->next = p->next;
        free(p);
        tmp = (node *)malloc(sizeof(node));
        tmp->x = 99;
        tmp->y = rand() % 11 + 5;
        tail->next = tmp;
        tmp->next = NULL;
        tail = tmp;
        ticker -= 10;  // 加速
        set_ticker(ticker);
    }
    // 绘制新的柱子
    for(p = head->next; p->next != NULL; p->x--, p = p->next)
    {
        // 使用 CHAR_BLANK 替代原先的 CHAR_STONE
        for(j = 0; j < p->y; j++)
        {
            move(j, p->x);
            addch(CHAR_BLANK);
            refresh();
        }
        for(j = p->y+5; j <= 23; j++)
        {
            move(j, p->x);
            addch(CHAR_BLANK);
            refresh();
        }

        if(p->x-10 >= 0 && p->x < 80)
        {
            for(j = 0; j < p->y; j++)
            {
                move(j, p->x-10);
                addch(CHAR_STONE);
                refresh();
            }
            for(j = p->y + 5; j <= 23; j++)
            {
                move(j, p->x-10);
                addch(CHAR_STONE);
                refresh();
            }
        }
    }
    tail->x--;
}
```

信号接收函数里将背景向前移动一列，同时让 bird 向下掉落一行，并检测 bird 是否撞到柱子，撞到的话就 game over 了。

#### 3.3 main() 函数

`main()` 函数里先调用初始化 `init()` ，然后进入 `while()` 循环体。循环中主要是三部分部分：

1. 判断用户的操作：如果是 `w` 键或空格键被按下，bird 就向上飞两行；如果按下的是 `q` 键则退出游戏；按下的是 `z` 键则暂停游戏；
2. 移动 bird 进行重绘；
3. 判断 bird 是否撞到柱子。

- 先看看代码：

```
int main()
{
    char ch;

    init();
    while(1)
    {
        ch = getch();  // 获取键盘输入
        if(ch == ' ' || ch == 'w' || ch == 'W')  // 按下空格或者 W 时
        {
            // 移动 bird ，并进行重绘
            move(bird_y, bird_x);
            addch(CHAR_BLANK);
            refresh();
            bird_y--;
            move(bird_y, bird_x);
            addch(CHAR_BIRD);
            refresh();

            // 如果 bird 撞到了柱子，结束游戏
            if((char)inch() == CHAR_STONE)
            {
                set_ticker(0);
                sleep(1);
                endwin();
                exit(0);
            }
        }
        else if(ch == 'z' || ch == 'Z')  // 暂停
        {
            set_ticker(0);
            do
            {
                ch = getch();
            } while(ch != 'z' && ch != 'Z');
            set_ticker(ticker);
        }
        else if(ch == 'q' || ch == 'Q')  // 退出
        {
            sleep(1);
            endwin();
            exit(0);
        }
    }
    return 0;
}
```

我们在 `main()` 函数里先做好初始化，然后在循环中接受键盘输入。如果是 `w` 键或空格键被按下，bird 就向上飞两行，如果按下的是 `q` 键则退出游戏，按下的是 `z` 键则暂停游戏。

- 下面看一下 `init()` 函数：

```c
void init()
{
    initscr();
    cbreak();
    noecho();
    curs_set(0);
    srand(time(0));
    signal(SIGALRM, drop);

    init_bird();
    init_head();
    init_wall();
    init_draw();
    sleep(1);
    ticker = 500;
    set_ticker(ticker);
}
```

`init()` 函数首先初始化屏幕，调用了 `ncurses` 提供的函数，然后调用各个子函数进行初始化。注意，我们安装了信号接收函数 `drop()` ，并且设定了定时时间。

各个初始化子函数如下。

- 初始化 bird 位置的 `init_bird()` 函数：

```c
void init_bird()
{
    bird_x = 5;
    bird_y = 15;
    move(bird_y, bird_x);
    addch(CHAR_BIRD);
    refresh();
    sleep(1);
}
```

- 初始化存放柱子的链表的 `init_head()` 及 `init_wall()` 函数:

```c
void init_head()
{
    Node tmp;

    tmp = (node *)malloc(sizeof(node));
    tmp->next = NULL;
    head = tmp;
    tail = head;
}
void init_wall()
{
    int i;
    Node tmp, p;

    p = head;
    for(i = 0; i < 5; i++)
    {
        tmp = (node *)malloc(sizeof(node));
        tmp->x = (i + 1) * 19;
        tmp->y = rand() % 11 + 5;
        p->next = tmp;
        tmp->next = NULL;
        p = tmp;
    }
   tail = p;
}
```

- 初始化屏幕:

```c
void init_draw()
{
    Node p;
    int i, j;

    // 遍历链表
    for(p = head->next; p->next != NULL; p = p->next)
    {
        // 绘制柱子
        for(i = p->x; i > p->x-10; i--)
        {
            for(j = 0; j < p->y; j++)
            {
                move(j, i);
                addch(CHAR_STONE);
            }
            for(j = p->y+5; j <= 23; j++)
            {
                move(j, i);
                addch(CHAR_STONE);
            }
        }
        refresh();
        sleep(1);
    }
}
```

到此，我们的 flappy_bird 游戏就编写完成了。

#### 3.4 编译

- 执行 `gcc` 指令进行编译：

```
gcc -o my_flappy_bird my_flappy_bird.c -lcurses
./my_flappy_bird
```

> 可能会出现报警信息：
>
> ![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190929-1569749018447)
>
> 它不会影响程序的编译，可以忽略。

![3.4-1](https://doc.shiyanlou.com/document-uid18510labid438timestamp1526550473166.png)

- 程序运行效果如下：

![3.4-2](https://doc.shiyanlou.com/document-uid18510labid438timestamp1526550489658.png)

### 四、 实验总结

本次实验课程我们使用 C 语言实现了一个基于字符界面的 Flappy bird 。同学们还可以在这门课的基础上进行改进，比如给柱子添加颜色，使柱子的宽度能随机变化等等。

直播回放

[蓝桥云课直播平台 - 蓝桥云课 (lanqiao.cn)](https://www.lanqiao.cn/live/180/)