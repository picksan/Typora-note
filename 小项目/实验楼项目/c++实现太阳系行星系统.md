# c++实现太阳系行星系统

[C++ 实现太阳系行星系统_C++ - 蓝桥云课 (lanqiao.cn)](https://www.lanqiao.cn/courses/558)

## 基本框架设计

### 实验介绍

本次实验将使用 OpenGL GLUT 编写一个简单的太阳系运行系统。

#### 实验知识点

- C++ 语言基础
- 基本的 Makefile
- 基本的 OOP 编程思想
- OpenGL GLUT 的结构基本使用

#### 实验效果图

运行效果如图所示：

![1-1-1](https://doc.shiyanlou.com/document-uid29879labid1884timestamp1465718291017.png)

### 基础知识

#### 认识 OpenGL 和 GLUT

OpenGL 包含了很多渲染函数，但是他们的设计目的是独立于任何窗口系统或操作系统的。因此，它自身并没有包含创建打开窗口或者从键盘或鼠标读取时间的函数，甚至连最基本的显示窗口的功能都没有，所以单纯只使用 OpenGL 是完全不可能创建一个完整的图形程序的。并且绝大多数程序都需要与用户进行交互（响应键盘鼠标等操作）。GLUT 则提供了这一便利。

GLUT 其实是 OpenGL Utility Toolkit 的缩写，它是一个处理 OpenGL 程序的工具库，主要负责处理与底层操作系统的调用及 I/O 操作。使用 GLUT 可以屏蔽掉底层操作系统 GUI 实现上的一些细节，仅使用 GLUT 的 API 即可跨平台的创建应用程序窗口、处理鼠标键盘事件等等。

我们先在实验楼环境中安装 GLUT：

```bash
sudo apt-get update && sudo apt-get install freeglut3 freeglut3-dev
```

一个标准的 GLUT 程序结构如下代码所示：

```c++
// 使用 GLUT 的基本头文件
#include <GL/glut.h>

// 创建图形窗口的基本宏
#define WINDOW_X_POS 50
#define WINDOW_Y_POS 50
#define WIDTH 700
#define HEIGHT 700

// 用于注册 GLUT 的回调
void onDisplay(void);
void onUpdate(void);
void onKeyboard(unsigned char key, int x, int y);

int main(int argc, char*  argv[]) {

    // 对 GLUT 进行初始化，并处理所有的命令行参数
    glutInit(&argc, argv);
    // 这个函数指定了使用 RGBA 模式还是颜色索引模式。另外还可以
    // 指定是使用单缓冲还是双缓冲窗口。这里我们使用 RGBA 和 双缓冲窗口
    glutInitDisplayMode(GLUT_RGBA |  GLUT_DOUBLE);
    // 设置窗口被创建时左上角位于屏幕上的位置
    glutInitWindowPosition(WINDOW_X_POS, WINDOW_Y_POS);
    // 设置窗口被创建时的宽高, 为了简便起见
    glutInitWindowSize(WIDTH, HEIGHT);
    // 创建一个窗口，输入的字符串为窗口的标题
    glutCreateWindow("SolarSystem at Shiyanlou");

    // glutDisplayFunc 的函数原型为 glutDisplayFunc(void (*func)(void))
    // 这是一个回调函数，每当 GLUT 确定一个窗口的内容需要更新显示的时候,
    // glutDisplayFunc 注册的回调函数就会被执行.
    //
    // glutIdleFunc(void (*func)(void)) 将指定一个函数，用于处理当事件循环
    // 处于空闲的时候，就执行这个函数。这个回调函数接受一个函数指针作为它的唯一参数
    //
    // glutKeyboardFunc(void (*func)(unsigned char key, int x, int y))
    // 会将键盘上的键与一个函数关联，当这个键被按下或者释放时，函数就会调用
    //
    // 因此下面的三行实际上是在向 GLUT 注册关键的三个回调函数
    glutDisplayFunc(onDisplay);
    glutIdleFunc(onUpdate);
    glutKeyboardFunc(onKeyboard);

    glutMainLoop();
    return 0;

}
```

在 `/home/shiyanlou/` 目录下新建 `main.cpp` 文件，并向其中写入如下代码：

```cpp
//
//  main.cpp
//  solarsystem
//
#include <GL/glut.h>
#include "solarsystem.hpp"

#define WINDOW_X_POS 50
#define WINDOW_Y_POS 50
#define WIDTH 700
#define HEIGHT 700

SolarSystem solarsystem;

void onDisplay(void) {
    solarsystem.onDisplay();
}
void onUpdate(void) {
    solarsystem.onUpdate();
}
void onKeyboard(unsigned char key, int x, int y) {
    solarsystem.onKeyboard(key, x, y);
}

int main(int argc, char*  argv[]) {
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_RGBA |  GLUT_DOUBLE);
    glutInitWindowPosition(WINDOW_X_POS, WINDOW_Y_POS);
    glutCreateWindow("SolarSystem at Shiyanlou");
    glutDisplayFunc(onDisplay);
    glutIdleFunc(onUpdate);
    glutKeyboardFunc(onKeyboard);
    glutMainLoop();
    return 0;
}
```

**提示**

- 单缓冲，是将所有的绘图指令在窗口上执行，就是直接在窗口上绘图，这样的绘图效率是比较慢的，如果使用单缓冲，而电脑处理性能不够，屏幕会出现闪烁状。
- 双缓冲，会将绘图指令是在一个缓冲区完成，这里的绘图非常的快，在绘图指令完成之后，再通过交换指令把完成的图形立即显示在屏幕上，进而避免出现绘图的不完整，效率很高。

双缓冲则主要分为前台缓冲和后台缓冲，前台缓冲即我们说看到的屏幕，后台缓冲则维护内存中，对用户不可见。使用双缓冲时所有绘图操作都会在后台进行，当完成绘制后，才会将结果复制到屏幕上。这么做的好处是，如果我们让绘制操作实时与显卡进行操作，当绘制任务复杂时，IO 操作同样会变得复杂，造成性能较低；而双缓冲只会在交换缓冲区时将绘制完成后的结果直接发送给显卡进行渲染，IO 显著降低。

在 OpenGL 中，推荐使用 GLfloat 来表示浮点数。

### 类设计

OOP 编程中首先要梳理清楚我们要处理的对象是什么。显然整个天体系统中，他们都是一颗星球(Star)，区别行星和恒星只需要通过它们是否具有父节点即可；其次，对于不同的星球而言，它们通常具有自身的材质，并且不同的材质会表现出自身是否发光，由此我们有了初步的对象模型。故我们将星球分为：普通能够自转并绕某个点公转的星球(Star), 具有特殊材质的星球(Planet), 能够发光的星球(LightPlanet)

此外，为了编程实现上的方便，我们要对现实世界的实际编程模型做一些前提假设：

1. 星球的运行轨道为圆形；
2. 自转速度保持相同；
3. 每次刷新绘制的时候假设时间经过了一天。

首先，我们可以考虑按下面的思路整个实现逻辑：

1. 初始化星球对象;
2. 初始化 OpenGL 引擎, 实现 onDraw 和 onUpdate;
3. 星球应该自己负责处理自己的属性、绕行关系、变换相关绘制，因此在设计星球的类时应该提供一个绘制 draw() 方法;
4. 星球也应该自己处理自己自转公转等更新显示的绘制，因此在设计星球类时候也应该提供一个更新方法 update();
5. 在 onDraw() 中应调 用星球的 draw() 方法;
6. 在 onUpdate() 中调用星球的 update() 方法;
7. 在 onKeyboard() 键盘调整整个太阳系的显示.

进一步，对于每个星球而言，都具有如下的属性：

1. 颜色 color
2. 公转半径 radius
3. 自转速度 selfSpeed
4. 公转速度 speed
5. 距离太阳中心的距离 distance
6. 绕行的星球 parentStar
7. 当前的自转的角度 alphaSelf
8. 当前的公转角度 alpha

在 `/home/shiyanlou/` 目录下新建 `stars.hpp` 文件，根据前面的分析，我们可以设计如下的类代码：

```c++
class Star {
public:
    // 星球的运行半径
    GLfloat radius;
    // 星球的公转、自传速度
    GLfloat speed, selfSpeed;
    // 星球的中心与父节点星球中心的距离
    GLfloat distance;
    // 星球的颜色
    GLfloat rgbaColor[4];

    // 父节点星球
    Star* parentStar;

    // 构造函数，构造一颗星球时必须提供
    // 旋转半径、旋转速度、自转速度、绕行(父节点)星球
    Star(GLfloat radius, GLfloat distance,
         GLfloat speed,  GLfloat selfSpeed,
         Star* parentStar);
    // 对一般的星球的移动、旋转等活动进行绘制
    void drawStar();
    // 提供默认实现，负责调用 drawStar()
    virtual void draw() { drawStar(); }
    // 参数为每次刷新画面时的时间跨度
    virtual void update(long timeSpan);
protected:
    GLfloat alphaSelf, alpha;
};
class Planet : public Star {
public:
    // 构造函数
    Planet(GLfloat radius, GLfloat distance,
           GLfloat speed,  GLfloat selfSpeed,
           Star* parentStar, GLfloat rgbColor[3]);
    // 增加对具备自身材质的行星绘制材质
    void drawPlanet();
    // 继续向其子类开放重写功能
    virtual void draw() { drawPlanet(); drawStar(); }
};
class LightPlanet : public Planet {
public:
    LightPlanet(GLfloat Radius, GLfloat Distance,
                GLfloat Speed,  GLfloat SelfSpeed,
                Star* ParentStar, GLfloat rgbColor[]);
    // 增加对提供光源的恒星绘制光照
    void drawLight();
    virtual void draw() { drawLight(); drawPlanet(); drawStar(); }
};
`
```

此外，我们还需要考虑太阳系类的设计。在太阳系中，太阳系显然是由各个行星组成的；并且，对于太阳系而言，太阳系中行星运动后的视图刷新应该由太阳系来完成。据此太阳系成员变量应为包含行星的变量，成员函数应作为处理太阳系内的视图刷新及键盘响应等事件，所以，在 `/home/shiyanlou/` 目录下新建 `solarsystem.hpp` 文件,在其中我们可以设计 `SolarSystem` 类：

```c++
class SolarSystem {

public:

    SolarSystem();
    ~SolarSystem();

    void onDisplay();
    void onUpdate();
    void onKeyboard(unsigned char key, int x, int y);

private:
    Star *stars[STARS_NUM];

    // 定义观察视角的参数
    GLdouble viewX, viewY, viewZ;
    GLdouble centerX, centerY, centerZ;
    GLdouble upX, upY, upZ;
};
```

**提示**

- 这里使用传统形式的数组来管理所有星球，而不是使用 C++中的 vector，是因为传统形式的数组就足够了
- 在 OpenGL 中定义观察视角是一个较为复杂的概念，需要一定篇幅进行解释，我们先在此记下定义观察视角至少需要九个参数，我们将在下一节中具体实现时再详细讲解它们的作用。

最后我们还需要考虑一下基本的参数和变量设置。

在 `SolarSystem` 中，包括太阳在内一共有九颗星球（不包括冥王星），但是在我们所设计的 Star 类中，每一个 Star 对象都具有 Star 的属性，因此我们还可以额外实现这些星球的卫星，比如围绕地球运行的月球，据此我们一共考虑实现十个星球。于是我们可以设置如下枚举，用于索引一个数组中的星球：

```c++
#define STARS_NUM 10
enum STARS {
    Sun,        // 太阳
    Mercury,    // 水星
    Venus,      // 金星
    Earth,      // 地球
    Moon,       // 月球
    Mars,       // 火星
    Jupiter,    // 木星
    Saturn,     // 土星
    Uranus,     // 天王星
    Neptune     // 海王星
};
Star * stars[STARS_NUM];
```

我们还假设了自转速度相同，使用一个宏来设置其速度，：

```c++
#define TIMEPAST 1
#define SELFROTATE 3
```

至此，将未实现的成员函数创建到对应的 `.cpp` 文件中，我们便完成了本节实验。

### 总结本节中的代码

我们来总结一下本节实验中需要完成的代码：

首先我们在 `main.cpp` 中创建了一个 `SolarSystm`，然后将显示刷新、空闲刷新及键盘事件的处理交给了 `glut`：

```c++
//
//  main.cpp
//  solarsystem
//
#include <GL/glut.h>
#include "solarsystem.hpp"

#define WINDOW_X_POS 50
#define WINDOW_Y_POS 50
#define WIDTH 700
#define HEIGHT 700

SolarSystem solarsystem;

void onDisplay(void) {
    solarsystem.onDisplay();
}
void onUpdate(void) {
    solarsystem.onUpdate();
}
void onKeyboard(unsigned char key, int x, int y) {
    solarsystem.onKeyboard(key, x, y);
}

int main(int argc, char*  argv[]) {
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_RGBA |  GLUT_DOUBLE);
    glutInitWindowPosition(WINDOW_X_POS, WINDOW_Y_POS);
    glutCreateWindow("SolarSystem at Shiyanlou");
    glutDisplayFunc(onDisplay);
    glutIdleFunc(onUpdate);
    glutKeyboardFunc(onKeyboard);
    glutMainLoop();
    return 0;
}
```

其次，我们在 `stars.hpp` 中分别创建了 `Star` `Planet` `LightPlanet` 类。

```c++
//
//  stars.hpp
//  solarsystem
//
//
#ifndef stars_hpp
#define stars_hpp

#include <GL/glut.h>

class Star {
public:
    GLfloat radius;
    GLfloat speed, selfSpeed;
    GLfloat distance;
    GLfloat rgbaColor[4];

    Star* parentStar;

    Star(GLfloat radius, GLfloat distance,
         GLfloat speed,  GLfloat selfSpeed,
         Star* parentStar);
    void drawStar();
    virtual void draw() { drawStar(); }
    virtual void update(long timeSpan);
protected:
    GLfloat alphaSelf, alpha;
};

class Planet : public Star {
public:
    Planet(GLfloat radius, GLfloat distance,
           GLfloat speed,  GLfloat selfSpeed,
           Star* parentStar, GLfloat rgbColor[3]);
    void drawPlanet();
    virtual void draw() { drawPlanet(); drawStar(); }
};

class LightPlanet : public Planet {
public:
    LightPlanet(GLfloat Radius, GLfloat Distance,
                GLfloat Speed,  GLfloat SelfSpeed,
                Star* parentStar, GLfloat rgbColor[]);
    void drawLight();
    virtual void draw() { drawLight(); drawPlanet(); drawStar(); }
};

#endif /* star_hpp */
```

在 `/home/shiyanlou/` 目录下新建 `stars.cpp` 文件，在其中填写`stars.hpp` 中对应类的成员函数实现:

```c++
//
//  stars.cpp
//  solarsystem
//
#include "stars.hpp"

#define PI 3.1415926535

Star::Star(GLfloat radius, GLfloat distance,
           GLfloat speed,  GLfloat selfSpeed,
           Star* parentStar) {
    // TODO:
}

void Star::drawStar() {
    // TODO:
}

void Star::update(long timeSpan) {
    // TODO:
}


Planet::Planet(GLfloat radius, GLfloat distance,
               GLfloat speed,  GLfloat selfSpeed,
               Star* parentStar, GLfloat rgbColor[3]) :
Star(radius, distance, speed, selfSpeed, parentStar) {
    // TODO:
}

void Planet::drawPlanet() {
    // TODO:
}

LightPlanet::LightPlanet(GLfloat radius,    GLfloat distance, GLfloat speed,
                         GLfloat selfSpeed, Star* parentStar,   GLfloat rgbColor[3]) :
Planet(radius, distance, speed, selfSpeed, parentStar, rgbColor) {
    // TODO:
}

void LightPlanet::drawLight() {
    // TODO:
}
```

在 `solarsystem.hpp` 中设计了 SolarSystem 类：

```c++
//
// solarsystem.hpp
// solarsystem
//
#include <GL/glut.h>

#include "stars.hpp"

#define STARS_NUM 10

class SolarSystem {

public:

    SolarSystem();
    ~SolarSystem();

    void onDisplay();
    void onUpdate();
    void onKeyboard(unsigned char key, int x, int y);

private:
    Star *stars[STARS_NUM];

    // 定义观察视角的参数
    GLdouble viewX, viewY, viewZ;
    GLdouble centerX, centerY, centerZ;
    GLdouble upX, upY, upZ;
};
```

在 `/home/shiyanlou/` 目录下新建 `solarsystem.cpp` 文件，并在其中实现对应的 `solarsystem.hpp` 中的成员函数:

```c++
//
//
// solarsystem
//
#include "solarsystem.hpp"

#define TIMEPAST 1
#define SELFROTATE 3

enum STARS {Sun, Mercury, Venus, Earth, Moon,
    Mars, Jupiter, Saturn, Uranus, Neptune};

void SolarSystem::onDisplay() {
    // TODO:
}
void SolarSystem::onUpdate() {
    // TODO:
}
void SolarSystem::onKeyboard(unsigned char key, int x, int y) {
    // TODO:
}
SolarSystem::SolarSystem() {
    // TODO:

}
SolarSystem::~SolarSystem() {
    // TODO:
}
```

在 `/home/shiyanlou/` 目录下新建 `Makefile` 文件，并向其中添加如下代码：

> 请手动输入，不要直接复制，注意不能使用空格键，只能使用 `<tab>` 键。

```txt
CXX = g++
EXEC = solarsystem
SOURCES = main.cpp stars.cpp solarsystem.cpp
OBJECTS = main.o stars.o solarsystem.o
LDFLAGS = -lglut -lGL -lGLU

all :
    $(CXX) $(SOURCES) $(LDFLAGS) -o $(EXEC)

clean:
    rm -f $(EXEC) *.gdb *.o
```

书写编译命令时注意 `-lglut -lGLU -lGL` 的位置，这是因为 g++ 编译器中 `-l` 选项用法有点特殊。

例如： `foo1.cpp -lz foo2.cpp`，如果目标文件 foo2.cpp 应用了库 z 中的函数，那么这些函数不会被直接加载。而如果 foo1.o 使用了 z 库中的函数则不会出现任何编译错误。

换句话说，整个链接的过程是自左向右的。当 foo1.cpp 中遇到无法解析的函数符号时，会查找右边的链接库，当发现选项 z 时，在 z 中查找然后发现函数，进而顺利完成链接。所以 `-l` 选项的库应该存在于所有编译文件的右边。

> 更多编译器细节请学习：g++/gdb 使用技巧

最后，在终端中运行：

```bash
make && ./solarsystem
```

运行结果如图所示：

![1-4-1](https://doc.shiyanlou.com/document-uid29879labid1883timestamp1465696617611.png)

可以看到，窗口已经创建出来了，但是之所以什么都没有(显示的是窗口背后的内容)，是因为我们还没有实现窗口中图形的刷新机制，在下一实验中我们再继续实现未完成的代码，让整个太阳系系统运行起来。

### 拓展阅读

1. D Shreiner. *OpenGL 编程指南*. 人民邮电出版社, 2005.

> 这本书详细介绍了 OpenGL 编程相关的方方面面，被誉为 『OpenGL 红宝书』。

## 编码实现

### 实验介绍

本节实验我们将对上一节实验中所设计的基本框架进行详细的实现。

#### 涉及的知识点

- OpenGL 的矩阵模式
- OpenGL 常用的图像绘制接口
- OpenGL 中的视角
- OpenGL 的光照实现

### 基础知识

这一小节主要讲解与本节实验相关的基础知识。

### OpenGL 中的矩阵概念

在线性代数中，我们知道了矩阵的概念，但实际上我们对矩阵的具体作用和认识并没有体会到多少。矩阵到底是什么？

首先我们看这样一个式子：

```c++
x = Ab
```

其中 `A` 为矩阵，`x,b` 为向量。

**观点一：**

x 和 b 都是 我们三维空间中的一个向量，那么 `A` 做了什么事情？———— `A` 把 `b` 变成了（变换到了） `x`。在这样的观点下，矩阵 A 可以被理解为一种变换。

再来看另一个式子：

```c++
Ax = By
```

其中 `A,B` 为矩阵, `x,y`为向量。

**观点二：**

对于两个不同的向量`x,y`来说，它们本质上是同一个东西，因为它们只需要乘以矩阵`A,B`就可以相等。在这样的观点下，矩阵 A 可以被理解为一种坐标系。换句话说，向量本身是独一无二的，但是我们因为要描述它，所以定义了一个坐标系。由于坐标系取得不同，向量的坐标也会发生变化，即对于同一个向量来说，在不同的坐标系下有着不同的坐标。 矩阵 A 恰好描述了一个坐标系，而 矩阵 B 也描述了一个坐标系，这两个坐标系作用到 `x,y`上之后，得到了相同的结果，也即 `x,y` 本质是同一个向量，只不过他们有着不同的坐标系。

综合上述两个观点，所以矩阵的本质是：**描述运动**。

于是在 OpenGL 内部有一个负责绘制变换的矩阵，这就是 OpenGL 中的矩阵模式。

正如前面所说，矩阵既能够描述运动的变换，也能够描述某个物体所处的坐标系，因此在处理不同的操作时，我们要给 OpenGL 设置不同的矩阵模式，这就需要用到

```c++
glMatrixMode()
```

这个函数接受三个不同的模式：`GL_PROJECTION` 投影, `GL_MODELVIEW` 模型视图, `GL_TEXTURE` 纹理。

`GL_PROJECTION` 会向 OpenGL 声明将进行投影操作，会把物体头投影到一个平面上。开启这个模式后要使用`glLoadIdentity()`把矩阵设置为单位矩阵，而后的操作如可以通过 `gluPerspective` 设置视景（在下面介绍完 OpenGL 中视角的概念后我们再详细说明这个函数的功能）。

`GL_MODELVIEW` 会向 OpenGL 声明接下来的语句将描绘一个以模型为基础的操作，比如设置摄像机视角，开启后同样需要设置 OpenGL 的矩阵模式为单位矩阵。

`GL_TEXTURE` 则是进行纹理相关的操作，我们暂时用不到。

> 如果对矩阵的概念不熟悉，可以直接将 glMatrixMode 理解为针对 OpenGL 申明接下来要做的事情。我们在绘制、旋转对象之前，一定要通过 glPushMatrix 保存当前的矩阵环境，否则会出现莫名其妙的绘制错误。

### 常用的 OpenGL 图像绘制 API

OpenGL 中提供了很多常用的与图形绘制相关的 API，这里我们挑几个常用的进行简单介绍，并在接下来的代码中进行使用：

- **glEnable(GLenum cap)**: 这个函数用于激活 OpenGL 中提供的各种功能，传入的参数 cap 是 OpenGL 内部的宏，提供诸如光源、雾化、抖动等效果；
- **glPushMatrix()** 和 **glPopMatrix()**: 将当前矩阵保存到堆栈栈顶（保存当前矩阵）；
- **glRotatef(alpha, x, y, z)**: 表示当前图形沿 `(x,y,z)` 逆时针旋转 alpha 度;
- **glTranslatef(distance, x, y)**: 表示当前图形沿 `(x,y)` 方向平移 distance 距离;
- **glutSolidSphere(GLdouble radius , GLint slices , GLint stacks)**: 绘制球体， radius 为半径，slices 为经线条数, stacks 为纬线条数;
- **glBegin()** 和 **glEnd()** ：当我们要进行图形绘制时，需要在开始绘制前和绘制完成后调用这两个函数，`glBegin()`指定了绘制图形的类型，例如 `GL_POINTS` 表示绘制点、 `GL_LINES` 表示绘制依次画出的点及他们之间的连线、`GL_TRIANGLES` 则是在每绘制的三个点中完成一个三角形、`GL_POLYGON` 则是绘制一个从第一个点到第 n 个点的多边形，等等。例如当我们需要绘制一个圆时可以边很多的多边形来模拟：

```c++
// r 是半径，n 是边数
glBegin(GL_POLYGON);
    for(i=0; i<n; ++i)
        glVertex2f(r*cos(2*PI/n*i), r*sin(2*PI/n*i));
glEnd();
```

### OpenGL 里的视角坐标

上一节中，我们在 `SolarSystem` 类中定义了九个成员变量

```c++
GLdouble viewX, viewY, viewZ;
GLdouble centerX, centerY, centerZ;
GLdouble upX, upY, upZ;
```

为了理解这九个变量，我们首先需要树立起 OpenGL 三维编程中摄像机视角的概念。

想象平时我们观看电影的画面其实都是由摄像机所在的视角拍摄完成的，因此 OpenGL 中也有类似的概念。如果我们把摄像机想象成我们自己的头，那么：

1. viewX, viewY, viewZ 就相当于头（摄像机）在 OpenGL 世界坐标中的坐标位置;
2. centerX, centerY, centerZ 则相当于头所看（摄像机所拍）物体的坐标位置;
3. upX, upY, upZ 则相当于头顶（摄像机顶部）朝上的方向向量（因为我们可以歪着头观察一个物体）。

至此，你便有了 OpenGL 中坐标系的概念。

我们约定本次实验的初始视角在 (x, -x, x) 处，则即有：

```c++
#define REST 700
#define REST_Y (-REST)
#define REST_Z (REST)
```

所观察物体（太阳）的位置在 (0,0,0)，则在 `SolarSystem` 类中的构造函数将视角初始化为：

```c++
viewX = 0;
viewY = REST_Y;
viewZ = REST_Z;
centerX = centerY = centerZ = 0;
upX = upY = 0;
upZ = 1;
```

则可以通过 `gluLookAt` 函数来设置视角的九个参数：

```c++
gluLookAt(viewX, viewY, viewZ, centerX, centerY, centerZ, upX, upY, upZ);
```

然后我们再来看 gluPerspective(GLdouble fovy,GLdouble aspect,GLdouble zNear,GLdouble zFar)。

这个函数会创建一个对称的透视型视景体，在使用这个函数前需要将 OpenGL 的矩阵模式设置为 GL_PROJECTION。

如下图所示:

![2-2.3-1](https://doc.shiyanlou.com/document-uid29879labid1884timestamp1465717984512.png)

在窗口中的画面是通过摄像机来捕捉的，捕捉的实际内容就是远平面上的内容，显示的内容则是近平面上的内容，所以，这个函数需要四个参数：

- 第一个参数为视角的大小
- 第二个参数为实际窗口的横纵比，如图中 `aspect=w/h`
- 第三个参数为近平面距离
- 第四个参数则为远平面距离

### OpenGL 里的光照效果

#### 基本概念

OpenGL 在处理光照时将光照系统分为了三个部分：光源、材质、光照环境。

顾名思义，光源就是光的来源，比如太阳； 材质这是指接受光照的各种物体的表面，比如太阳系中除太阳以外的行星和卫星都是这里所指的材质； 光照环境则是一些额外的参数，他们讲应将最终得到的光照面积，比如光线通常会经过多次反射，这时制定一个『环境亮度』的参数，可以使得最后的画面接近真实情况。

物理学里，平行光射入光滑平面上后所得的反射光依然是平行光，这种反射被叫做『镜面反射』；而对于不光滑的平面造成的反射，便就是所谓的『漫反射』。

![2-2.4-1](https://doc.shiyanlou.com/document-uid29879labid1884timestamp1465717994674.png)

#### 光源

在 OpenGL 里要实现光照系统，我们首先需要做的就是设置光源。值得一提的是，OpenGL 内只支持有限数量的光源（八个）分别使用 `GL_LIGHT0` 至 `GL_LIGHT7` 这八个宏来表示。通过 `glEnable` 函数来启用， `glDisable` 函数来禁用。例如：`glEnable(GL_LIGHT0);`

设置光源位置则需要使用 `glMaterialfv`进行设置，例如：

```c++
GLfloat light_position[] = {0.0f, 0.0f, 0.0f, 1.0f};
glLightfv(GL_LIGHT0, GL_POSITION, light_position); // 指定零号光源的位置
```

这里的位置由四个值来表示，`(x,y,z,w)` 其中当 `w`为 0 时，表示该光源位于无限远，而 `x,y,z` 便指定了这个无限远光源的方向； 当 `w` 不为 0 时，表示 位置性光源，其位置为 `(x/w, y/w, z/w)` 。

#### 材质

设置一个物体的材质一般有五个属性需要设置：

1. 多次反射后追踪在环境中遗留的光照强度;
2. 漫反射后的光照强度;
3. 镜面反射后的光照强度;
4. OpenGL 中不发光物体发出的微弱且不影像其他物体的光照强度;
5. 镜面指数，指越小，表示材质越粗糙，点光源发射的光线照射后，产生较大的亮点；相反，值越大，材质越像镜面，产生较小的亮点。

设置材质 OpenGL 提供了两个版本的函数：

```c++
void glMaterialf(GLenum face, GLenum pname, TYPE param);
void glMaterialfv(GLenum face, GLenum pname, TYPE *param);
```

其差异在于，镜面指数只需要设置一个数值，这时只需要使用 `glMaterialf`；而其他的材质设置都需要设置多个值，这是需要使用数组进行设置，使用带指针向量参数的版本 `glMaterialfv`，例如：

```c++
GLfloat mat_ambient[]  = {0.0f, 0.0f, 0.5f, 1.0f};
GLfloat mat_diffuse[]  = {0.0f, 0.0f, 0.5f, 1.0f};
GLfloat mat_specular[] = {0.0f, 0.0f, 1.0f, 1.0f};
GLfloat mat_emission[] = {0.5f, 0.5f, 0.5f, 0.5f};
GLfloat mat_shininess  = 90.0f;
glMaterialfv(GL_FRONT, GL_AMBIENT,   mat_ambient);
glMaterialfv(GL_FRONT, GL_DIFFUSE,   mat_diffuse);
glMaterialfv(GL_FRONT, GL_SPECULAR,  mat_specular);
glMaterialfv(GL_FRONT, GL_EMISSION,  mat_emission);
glMaterialf (GL_FRONT, GL_SHININESS, mat_shininess);
```

#### 光照环境

OpenGL 是默认关闭光照处理的，要打开光照处理功能，需要使用 `GL_LIGHTING` 宏来激活，即 `glEnable(GL_LIGHTING);`。

### 实现

本节将通过实践操作，带领大家实现太阳系行星系统。

### 行星的绘制

行星在绘制时，首先要考虑自身的公转角度和自转角度，因此我们可以先在 `/home/shiyanlou/stars.cpp` 文件中实现星球类的`Star::update(long timeSpan)` 成员函数：

```c++
void Star::update(long timeSpan) {
    alpha += timeSpan * speed;  // 更新角度
    alphaSelf += selfSpeed;     // 更新自转角度
}
```

在完成公转和自转角度的更新后，我们便可以根据参数绘制具体的星球了：

```c++
void Star::drawStar() {

    glEnable(GL_LINE_SMOOTH);
    glEnable(GL_BLEND);

    int n = 1440;

    // 保存 OpenGL 当前的矩阵环境
    glPushMatrix();
    {
        // 公转

        // 如果是行星，且距离不为0，那么 且向原点平移一个半径
        // 这部分用于处理卫星
        if (parentStar != 0 && parentStar->distance > 0) {
            //将绘制的图形沿 z 轴旋转 alpha
            glRotatef(parentStar->alpha, 0, 0, 1);
            // x 轴方向上平移 distance , y,z 方向不变
            glTranslatef(parentStar->distance, 0.0, 0.0);
        }
        // 绘制运行轨道
        glBegin(GL_LINES);
        for(int i=0; i<n; ++i)
            glVertex2f(distance * cos(2 * PI * i / n),
                       distance * sin(2 * PI * i / n));
        glEnd();
        // 绕 z 轴旋转 alpha
        glRotatef(alpha, 0, 0, 1);
        // x 轴方向平移 distance, y,z 方向不变
        glTranslatef(distance, 0.0, 0.0);

        // 自转
        glRotatef(alphaSelf, 0, 0, 1);

        // 绘制行星颜色
        glColor3f(rgbaColor[0], rgbaColor[1], rgbaColor[2]);
        glutSolidSphere(radius, 40, 32);
    }
    // 恢复绘制前的矩阵环境
    glPopMatrix();

}
```

> 这里用到了 sin() 和 cos() 函数，需要引入 `#include<cmath>`

### 光照的绘制

对于 `Planet` 类而言，属于不发光的星球，我们要绘制它的光照效果，在 `/home/shiyanlou/stars.cpp` 文件中添加如下代码：

```c++
void Planet::drawPlanet() {
    GLfloat mat_ambient[]  = {0.0f, 0.0f, 0.5f, 1.0f};
    GLfloat mat_diffuse[]  = {0.0f, 0.0f, 0.5f, 1.0f};
    GLfloat mat_specular[] = {0.0f, 0.0f, 1.0f, 1.0f};
    GLfloat mat_emission[] = {rgbaColor[0], rgbaColor[1], rgbaColor[2], rgbaColor[3]};
    GLfloat mat_shininess  = 90.0f;

    glMaterialfv(GL_FRONT, GL_AMBIENT,   mat_ambient);
    glMaterialfv(GL_FRONT, GL_DIFFUSE,   mat_diffuse);
    glMaterialfv(GL_FRONT, GL_SPECULAR,  mat_specular);
    glMaterialfv(GL_FRONT, GL_EMISSION,  mat_emission);
    glMaterialf (GL_FRONT, GL_SHININESS, mat_shininess);
}
```

而对于 `LightPlanet` 类来说，属于发光的星球，所以我们不但要设置其光照材质，还要设置其光源位置：

```c++
void LightPlanet::drawLight() {

    GLfloat light_position[] = {0.0f, 0.0f, 0.0f, 1.0f};
    GLfloat light_ambient[]  = {0.0f, 0.0f, 0.0f, 1.0f};
    GLfloat light_diffuse[]  = {1.0f, 1.0f, 1.0f, 1.0f};
    GLfloat light_specular[] = {1.0f, 1.0f, 1.0f, 1.0f};
    glLightfv(GL_LIGHT0, GL_POSITION, light_position); // 指定零号光源的位置
    glLightfv(GL_LIGHT0, GL_AMBIENT,  light_ambient);  // 表示各种光线照射到该材质上，经过很多次反射后追踪遗留在环境中的光线强度
    glLightfv(GL_LIGHT0, GL_DIFFUSE,  light_diffuse);  // 漫反射后的光照强度
    glLightfv(GL_LIGHT0, GL_SPECULAR, light_specular); // 镜面反射后的光照强度

}
```

### 实现窗口的绘制

在上一节实验中我们提到过 `glutDisplayFunc` 、`glutIdleFunc` 这两个处理图像显示的最重要的函数，`glutDisplayFunc` 会在 GLUT 确定窗口内容需要更新的时候将回调函数执行，`glutIdleFunc`则是处理当事件循环空闲时的回调。

我们要实现整个太阳系运动起来，就应该考虑在什么时候更新行星的位置，在什么时候刷新视图。

显然，`glutDisplayFunc` 应该专注于负责刷新视图显示，当事件空闲时，我们便可以开始更新星球的位置，当位置更新完毕后，再调用视图刷新函数进行刷新。

因此，我们可以先实现 `glutDisplayFunc`中调用的 `SolarSystem` 类中的成员函数 `SolarSystem::onUpdate()`：

```c++
#define TIMEPAST 1 // 假设每次更新都经过了一天
void SolarSystem::onUpdate() {

    for (int i=0; i<STARS_NUM; i++)
        stars[i]->update(TIMEPAST); // 更新星球的位置

    this->onDisplay(); // 刷新显示
}
```

其次，对于显示视图的刷新则是实现`SolarSystem::onDisplay()`：

```c++
void SolarSystem::onDisplay() {

    // 清除 viewport 缓冲区
    glClear(GL_COLOR_BUFFER_BIT  |  GL_DEPTH_BUFFER_BIT);
    // 清空并设置颜色缓存
    glClearColor(.7f, .7f, .7f, .1f);
    // 指定当前矩阵为投影矩阵
    glMatrixMode(GL_PROJECTION);
    // 将指定的矩阵指定为单位矩阵
    glLoadIdentity();
    // 指定当前的观察视景体
    gluPerspective(75.0f, 1.0f, 1.0f, 40000000);
    // 指定当前矩阵为视景矩阵堆栈应用术后的矩阵操作
    glMatrixMode(GL_MODELVIEW);
    // 指定当前的矩阵为单位矩阵
    glLoadIdentity();
    // 定义视图矩阵，并与当前矩阵相乘
    gluLookAt(viewX, viewY, viewZ, centerX, centerY, centerZ, upX, upY, upZ);

    // 设置第一个光源(0号光源)
    glEnable(GL_LIGHT0);
    // 启用光源
    glEnable(GL_LIGHTING);
    // 启用深度测试，根据坐标的远近自动隐藏被遮住的图形
    glEnable(GL_DEPTH_TEST);

    // 绘制星球
    for (int i=0; i<STARS_NUM; i++)
        stars[i]->draw();

    // 我们在 main 函数中初始化显示模式时使用了 GLUT_DOUBLE
    // 需要使用 glutSwapBuffers 在绘制结束后实现双缓冲的缓冲区交换
    glutSwapBuffers();
}
```

### 类的构造函数和析构函数

`stars.hpp` 中所定义类的构造函数需要对类中的成员变量进行初始化，相对较为简单，甚至可以使用默认析构，因此这部分请读者自行实现这些构造函数：

```c++
Star::Star(GLfloat radius, GLfloat distance,
           GLfloat speed,  GLfloat selfSpeed,
           Star* parent);
Planet::Planet(GLfloat radius, GLfloat distance,
               GLfloat speed,  GLfloat selfSpeed,
               Star* parent, GLfloat rgbColor[3]);
LightPlanet::LightPlanet(GLfloat radius, GLfloat distance,
                         GLfloat speed,  GLfloat selfSpeed,
                         Star* parent,   GLfloat rgbColor[3]);
```

> **提示**：注意在初始化速度变量时将其转化为角速度 其转换公式为：alpha_speed = 360/speed

对于`solarsystem.cpp`的构造函数，我们需要对所有的星球进行初始化，这里为了方便起见我们先给出适当的行星之间的参数：

```c++
// 公转半径
#define SUN_RADIUS 48.74
#define MER_RADIUS  7.32
#define VEN_RADIUS 18.15
#define EAR_RADIUS 19.13
#define MOO_RADIUS  6.15
#define MAR_RADIUS 10.19
#define JUP_RADIUS 42.90
#define SAT_RADIUS 36.16
#define URA_RADIUS 25.56
#define NEP_RADIUS 24.78

// 距太阳的距离
#define MER_DIS   62.06
#define VEN_DIS  115.56
#define EAR_DIS  168.00
#define MOO_DIS   26.01
#define MAR_DIS  228.00
#define JUP_DIS  333.40
#define SAT_DIS  428.10
#define URA_DIS 848.00
#define NEP_DIS 949.10

// 运动速度
#define MER_SPEED   87.0
#define VEN_SPEED  225.0
#define EAR_SPEED  365.0
#define MOO_SPEED   30.0
#define MAR_SPEED  687.0
#define JUP_SPEED 1298.4
#define SAT_SPEED 3225.6
#define URA_SPEED 3066.4
#define NEP_SPEED 6014.8

// 自转速度
#define SELFROTATE 3

// 为了方便操作数组，定义一个设置多为数组的宏
#define SET_VALUE_3(name, value0, value1, value2) \
                   ((name)[0])=(value0), ((name)[1])=(value1), ((name)[2])=(value2)

// 在上一节实验中我们定义了星球的枚举
enum STARS {Sun, Mercury, Venus, Earth, Moon,
    Mars, Jupiter, Saturn, Uranus, Neptune};
```

> **提示**

> 我们在这里定义了一个 `SET_VALUE_3` 的宏，读者或许会认为我们可以编写一个函数来达到快速设置的目的

> 事实上，宏会在编译过程就完成整体的替换工作，而定义函数

> 这需要在调用期间进行函数的堆栈操作，性能远不及编译过程就完成的宏处理

> 因此，使用宏会变得更加高效

> 但是值得注意的是，虽然宏能够变得更加高效，但过分的滥用则会造成代码的丑陋及弱可读性，而适当的使用宏则是可以提倡的

因此我们可以实现`SolarSystem`类的构造函数，其中星球的颜色是随机选取的，读者可以自行更改星球的颜色：

```c++
SolarSystem::SolarSystem() {

    // 定义视角，在前面我们已经讨论过视角的初始化了
    viewX = 0;
    viewY = REST_Y;
    viewZ = REST_Z;
    centerX = centerY = centerZ = 0;
    upX = upY = 0;
    upZ = 1;

    // 太阳
    GLfloat rgbColor[3] = {1, 0, 0};
    stars[Sun]     = new LightPlanet(SUN_RADIUS, 0, 0, SELFROTATE, 0, rgbColor);
    // 水星
    SET_VALUE_3(rgbColor, .2, .2, .5);
    stars[Mercury] = new Planet(MER_RADIUS, MER_DIS, MER_SPEED, SELFROTATE, stars[Sun], rgbColor);
    // 金星
    SET_VALUE_3(rgbColor, 1, .7, 0);
    stars[Venus]   = new Planet(VEN_RADIUS, VEN_DIS, VEN_SPEED, SELFROTATE, stars[Sun], rgbColor);
    // 地球
    SET_VALUE_3(rgbColor, 0, 1, 0);
    stars[Earth]   = new Planet(EAR_RADIUS, EAR_DIS, EAR_SPEED, SELFROTATE, stars[Sun], rgbColor);
    // 月亮
    SET_VALUE_3(rgbColor, 1, 1, 0);
    stars[Moon]    = new Planet(MOO_RADIUS, MOO_DIS, MOO_SPEED, SELFROTATE, stars[Earth], rgbColor);
    // 火星
    SET_VALUE_3(rgbColor, 1, .5, .5);
    stars[Mars]    = new Planet(MAR_RADIUS, MAR_DIS, MAR_SPEED, SELFROTATE, stars[Sun], rgbColor);
    // 木星
    SET_VALUE_3(rgbColor, 1, 1, .5);
    stars[Jupiter] = new Planet(JUP_RADIUS, JUP_DIS, JUP_SPEED, SELFROTATE, stars[Sun], rgbColor);
    // 土星
    SET_VALUE_3(rgbColor, .5, 1, .5);
    stars[Saturn]  = new Planet(SAT_RADIUS, SAT_DIS, SAT_SPEED, SELFROTATE, stars[Sun], rgbColor);
    // 天王星
    SET_VALUE_3(rgbColor, .4, .4, .4);
    stars[Uranus]  = new Planet(URA_RADIUS, URA_DIS, URA_SPEED, SELFROTATE, stars[Sun], rgbColor);
    // 海王星
    SET_VALUE_3(rgbColor, .5, .5, 1);
    stars[Neptune] = new Planet(NEP_RADIUS, NEP_DIS, NEP_SPEED, SELFROTATE, stars[Sun], rgbColor);

}
```

此外，不要忘了在析构函数中释放申请的内存：

```c++
SolarSystem::~SolarSystem() {
    for(int i = 0; i<STARS_NUM; i++)
        delete stars[i];
}
```

### 键盘按键变换视角的实现

我们不妨用键盘上的 `w,a,s,d,x` 五个键来控制视角的变换，并使用 `r` 键来复位视角。首先我们要确定一次按键后视角的变化大小，这里我们先定义一个宏 `OFFSET`。然后通过传入的 `key` 来判断用户的按键行为

```c++
#define OFFSET 20
void SolarSystem::onKeyboard(unsigned char key, int x, int y) {

    switch (key)    {
        case 'w': viewY += OFFSET; break; // 摄像机Y 轴位置增加 OFFSET
        case 's': viewZ += OFFSET; break;
        case 'S': viewZ -= OFFSET; break;
        case 'a': viewX -= OFFSET; break;
        case 'd': viewX += OFFSET; break;
        case 'x': viewY -= OFFSET; break;
        case 'r':
            viewX = 0; viewY = REST_Y; viewZ = REST_Z;
            centerX = centerY = centerZ = 0;
            upX = upY = 0; upZ = 1;
            break;
        case 27: exit(0); break;
        default: break;
    }

}
```

### 总结本节中的代码(供参考)

本节实验中我们主要实现了 `stars.cpp` 和 `solarsystem.cpp` 这两个文件中的代码。

`stars.cpp` 的代码如下：

```c++
//
//  stars.cpp
//  solarsystem
//

#include "stars.hpp"
#include <cmath>

#define PI 3.1415926535

Star::Star(GLfloat radius, GLfloat distance,
           GLfloat speed,  GLfloat selfSpeed,
           Star* parent) {
    this->radius = radius;
    this->selfSpeed = selfSpeed;
    this->alphaSelf = this->alpha = 0;
    this->distance = distance;

    for (int i = 0; i < 4; i++)
        this->rgbaColor[i] = 1.0f;

    this->parentStar = parent;
    if (speed > 0)
        this->speed = 360.0f / speed;
    else
        this->speed = 0.0f;
}

void Star::drawStar() {

    glEnable(GL_LINE_SMOOTH);
    glEnable(GL_BLEND);

    int n = 1440;

    glPushMatrix();
    {
        if (parentStar != 0 && parentStar->distance > 0) {
            glRotatef(parentStar->alpha, 0, 0, 1);
            glTranslatef(parentStar->distance, 0.0, 0.0);
        }
        glBegin(GL_LINES);
        for(int i=0; i<n; ++i)
            glVertex2f(distance * cos(2 * PI * i / n),
                       distance * sin(2 * PI * i / n));
        glEnd();
        glRotatef(alpha, 0, 0, 1);
        glTranslatef(distance, 0.0, 0.0);

        glRotatef(alphaSelf, 0, 0, 1);

        glColor3f(rgbaColor[0], rgbaColor[1], rgbaColor[2]);
        glutSolidSphere(radius, 40, 32);
    }
    glPopMatrix();

}

void Star::update(long timeSpan) {
    alpha += timeSpan * speed;
    alphaSelf += selfSpeed;
}


Planet::Planet(GLfloat radius, GLfloat distance,
               GLfloat speed,  GLfloat selfSpeed,
               Star* parent, GLfloat rgbColor[3]) :
Star(radius, distance, speed, selfSpeed, parent) {
    rgbaColor[0] = rgbColor[0];
    rgbaColor[1] = rgbColor[1];
    rgbaColor[2] = rgbColor[2];
    rgbaColor[3] = 1.0f;
}

void Planet::drawPlanet() {
    GLfloat mat_ambient[]  = {0.0f, 0.0f, 0.5f, 1.0f};
    GLfloat mat_diffuse[]  = {0.0f, 0.0f, 0.5f, 1.0f};
    GLfloat mat_specular[] = {0.0f, 0.0f, 1.0f, 1.0f};
    GLfloat mat_emission[] = {rgbaColor[0], rgbaColor[1], rgbaColor[2], rgbaColor[3]};
    GLfloat mat_shininess  = 90.0f;

    glMaterialfv(GL_FRONT, GL_AMBIENT,   mat_ambient);
    glMaterialfv(GL_FRONT, GL_DIFFUSE,   mat_diffuse);
    glMaterialfv(GL_FRONT, GL_SPECULAR,  mat_specular);
    glMaterialfv(GL_FRONT, GL_EMISSION,  mat_emission);
    glMaterialf (GL_FRONT, GL_SHININESS, mat_shininess);
}

LightPlanet::LightPlanet(GLfloat radius,    GLfloat distance, GLfloat speed,
                         GLfloat selfSpeed, Star* parent,   GLfloat rgbColor[3]) :
Planet(radius, distance, speed, selfSpeed, parent, rgbColor) {
    ;
}

void LightPlanet::drawLight() {

    GLfloat light_position[] = {0.0f, 0.0f, 0.0f, 1.0f};
    GLfloat light_ambient[]  = {0.0f, 0.0f, 0.0f, 1.0f};
    GLfloat light_diffuse[]  = {1.0f, 1.0f, 1.0f, 1.0f};
    GLfloat light_specular[] = {1.0f, 1.0f, 1.0f, 1.0f};
    glLightfv(GL_LIGHT0, GL_POSITION, light_position);
    glLightfv(GL_LIGHT0, GL_AMBIENT,  light_ambient);
    glLightfv(GL_LIGHT0, GL_DIFFUSE,  light_diffuse);
    glLightfv(GL_LIGHT0, GL_SPECULAR, light_specular);

}
```

`solarsystem.cpp` 的代码如下：

```c++
//
// solarsystem.cpp
// solarsystem
//

#include "solarsystem.hpp"

#define REST 700
#define REST_Z (REST)
#define REST_Y (-REST)

void SolarSystem::onDisplay() {

    glClear(GL_COLOR_BUFFER_BIT  |  GL_DEPTH_BUFFER_BIT);
    glClearColor(.7f, .7f, .7f, .1f);
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluPerspective(75.0f, 1.0f, 1.0f, 40000000);
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
    gluLookAt(viewX, viewY, viewZ, centerX, centerY, centerZ, upX, upY, upZ);

    glEnable(GL_LIGHT0);
    glEnable(GL_LIGHTING);
    glEnable(GL_DEPTH_TEST);

    for (int i=0; i<STARS_NUM; i++)
        stars[i]->draw();

    glutSwapBuffers();
}

#define TIMEPAST 1
void SolarSystem::onUpdate() {

    for (int i=0; i<STARS_NUM; i++)
        stars[i]->update(TIMEPAST);

    this->onDisplay();
}

#define OFFSET 20
void SolarSystem::onKeyboard(unsigned char key, int x, int y) {

    switch (key)    {
        case 'w': viewY += OFFSET; break;
        case 's': viewZ += OFFSET; break;
        case 'S': viewZ -= OFFSET; break;
        case 'a': viewX -= OFFSET; break;
        case 'd': viewX += OFFSET; break;
        case 'x': viewY -= OFFSET; break;
        case 'r':
            viewX = 0; viewY = REST_Y; viewZ = REST_Z;
            centerX = centerY = centerZ = 0;
            upX = upY = 0; upZ = 1;
            break;
        case 27: exit(0); break;
        default: break;
    }

}

#define SUN_RADIUS 48.74
#define MER_RADIUS  7.32
#define VEN_RADIUS 18.15
#define EAR_RADIUS 19.13
#define MOO_RADIUS  6.15
#define MAR_RADIUS 10.19
#define JUP_RADIUS 42.90
#define SAT_RADIUS 36.16
#define URA_RADIUS 25.56
#define NEP_RADIUS 24.78

#define MER_DIS   62.06
#define VEN_DIS  115.56
#define EAR_DIS  168.00
#define MOO_DIS   26.01
#define MAR_DIS  228.00
#define JUP_DIS  333.40
#define SAT_DIS  428.10
#define URA_DIS 848.00
#define NEP_DIS 949.10

#define MER_SPEED   87.0
#define VEN_SPEED  225.0
#define EAR_SPEED  365.0
#define MOO_SPEED   30.0
#define MAR_SPEED  687.0
#define JUP_SPEED 1298.4
#define SAT_SPEED 3225.6
#define URA_SPEED 3066.4
#define NEP_SPEED 6014.8

#define SELFROTATE 3

enum STARS {Sun, Mercury, Venus, Earth, Moon,
    Mars, Jupiter, Saturn, Uranus, Neptune};

#define SET_VALUE_3(name, value0, value1, value2) \
                   ((name)[0])=(value0), ((name)[1])=(value1), ((name)[2])=(value2)

SolarSystem::SolarSystem() {

    viewX = 0;
    viewY = REST_Y;
    viewZ = REST_Z;
    centerX = centerY = centerZ = 0;
    upX = upY = 0;
    upZ = 1;

    GLfloat rgbColor[3] = {1, 0, 0};
    stars[Sun]     = new LightPlanet(SUN_RADIUS, 0, 0, SELFROTATE, 0, rgbColor);

    SET_VALUE_3(rgbColor, .2, .2, .5);
    stars[Mercury] = new Planet(MER_RADIUS, MER_DIS, MER_SPEED, SELFROTATE, stars[Sun], rgbColor);

    SET_VALUE_3(rgbColor, 1, .7, 0);
    stars[Venus]   = new Planet(VEN_RADIUS, VEN_DIS, VEN_SPEED, SELFROTATE, stars[Sun], rgbColor);

    SET_VALUE_3(rgbColor, 0, 1, 0);
    stars[Earth]   = new Planet(EAR_RADIUS, EAR_DIS, EAR_SPEED, SELFROTATE, stars[Sun], rgbColor);

    SET_VALUE_3(rgbColor, 1, 1, 0);
    stars[Moon]    = new Planet(MOO_RADIUS, MOO_DIS, MOO_SPEED, SELFROTATE, stars[Earth], rgbColor);

    SET_VALUE_3(rgbColor, 1, .5, .5);
    stars[Mars]    = new Planet(MAR_RADIUS, MAR_DIS, MAR_SPEED, SELFROTATE, stars[Sun], rgbColor);

    SET_VALUE_3(rgbColor, 1, 1, .5);
    stars[Jupiter] = new Planet(JUP_RADIUS, JUP_DIS, JUP_SPEED, SELFROTATE, stars[Sun], rgbColor);

    SET_VALUE_3(rgbColor, .5, 1, .5);
    stars[Saturn]  = new Planet(SAT_RADIUS, SAT_DIS, SAT_SPEED, SELFROTATE, stars[Sun], rgbColor);

    SET_VALUE_3(rgbColor, .4, .4, .4);
    stars[Uranus]  = new Planet(URA_RADIUS, URA_DIS, URA_SPEED, SELFROTATE, stars[Sun], rgbColor);

    SET_VALUE_3(rgbColor, .5, .5, 1);
    stars[Neptune] = new Planet(NEP_RADIUS, NEP_DIS, NEP_SPEED, SELFROTATE, stars[Sun], rgbColor);

}
SolarSystem::~SolarSystem() {
    for(int i = 0; i<STARS_NUM; i++)
        delete stars[i];
}
```

在终端中运行：

```bash
make && ./solarsystem
```

运行结果如图所示：

![2-4-1](https://doc.shiyanlou.com/document-uid29879labid1884timestamp1465718291017.png)

由于星球的颜色是单一的，光照效果不够明显但依然能够看到，诸如黄色的木星右侧可以看到有泛白，此外，我们还可以通过键盘来调整太阳系的查看视角：

![2-4-2](https://doc.shiyanlou.com/document-uid29879labid1884timestamp1465718628334.png)

本课程相关代码：

```bash
wget http://labfile.oss.aliyuncs.com/courses/558/solarsystem.zip
```

#### 拓展阅读

1. D Shreiner.

    

   OpenGL 编程指南

   . 人民邮电出版社, 2005. 2.

   > 这本书详细介绍了 OpenGL 编程相关的方方面面，被誉为 『OpenGL 红宝书』