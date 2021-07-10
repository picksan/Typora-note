# Python实现图片转字符画

字符画是一系列字符的组合，我们可以把字符看作是比较大块的像素，一个字符能表现一种颜色（为了简化可以这么理解），字符的种类越多，可以表现的颜色也越多，图片也会更有层次感。

问题来了，我们是要转换一张彩色的图片，这么多的颜色，要怎么对应到单色的字符画上去？这里就要介绍灰度值的概念了。

> 灰度值：指黑白图像中点的颜色深度，范围一般从0到255，白色为255，黑色为0，故黑白图片也称灰度图像。

另外一个概念是 RGB 色彩：

> RGB色彩模式是工业界的一种颜色标准，是通过对红(R)、绿(G)、蓝(B)三个颜色通道的变化以及它们相互之间的叠加来得到各式各样的颜色的，RGB即是代表红、绿、蓝三个通道的颜色，这个标准几乎包括了人类视力所能感知的所有颜色，是目前运用最广的颜色系统之一。- 来自百度百科介绍

我们可以使用灰度值公式将像素的 RGB 值映射到灰度值（注意这个公式并不是一个真实的算法，而是简化的 sRGB IEC61966-2.1 公式，真实的公式更复杂一些，不过在我们的这个应用场景下并没有必要）：

```
gray ＝ 0.2126 * r + 0.7152 * g + 0.0722 * b
```

这样就好办了，我们可以创建一个不重复的字符列表，灰度值小（暗）的用列表开头的符号，灰度值大（亮）的用列表末尾的符号。

### 实验步骤

首先，安装 Python 图像处理库 pillow（PIL）：

```sh
$ sudo pip3 install --upgrade pip
$ sudo pip3 install pillow
```

然后在`/home/shiyanlou/` 目录下创建 `ascii.py` 代码文件进行编辑：

```sh
$ cd /home/shiyanlou/
$ touch ascii.py
```

### 编写代码

使用 vim 或者 gedit 打开代码文件：

```
$ cd /home/shiyanlou
$ gedit ascii.py
```

文件打开后依次输入以下的代码内容。

首先导入必要的库，argparse 库是用来管理命令行参数输入的

```python
from PIL import Image
import argparse
```

### 处理命令行参数

我们首先使用 argparse 处理命令行参数，目标是获取输入的图片路径、输出字符画的宽和高以及输出文件的路径：

```python
# 首先，构建命令行输入参数处理 ArgumentParser 实例
parser = argparse.ArgumentParser()

# 定义输入文件、输出文件、输出字符画的宽和高
parser.add_argument('file')     #输入文件
parser.add_argument('-o', '--output')   #输出文件
parser.add_argument('--width', type = int, default = 80) #输出字符画宽
parser.add_argument('--height', type = int, default = 80) #输出字符画高

# 解析并获取参数
args = parser.parse_args()

# 输入的图片文件路径
IMG = args.file

# 输出字符画的宽度
WIDTH = args.width

# 输出字符画的高度
HEIGHT = args.height

# 输出字符画的路径
OUTPUT = args.output
```

### 实现RGB值转字符的函数

首先将 RGB 值转为灰度值，然后使用灰度值映射到字符列表中的某个字符。

下面是我们的字符画所使用的字符集，一共有 70 个字符，为了方便写入到实验环境中，可以使用实验环境右边工具栏上的[剪切板](https://www.shiyanlou.com/library/shiyanlou-docs/feature/clipboard.md)将以下代码内容拷贝到实验环境中，注意需要使用右键复制和拷贝，不要使用 `Ctrl-C/Ctrl-V` 快捷键。字符的种类与数量可以自己根据字符画的效果反复调试：

```python
ascii_char = list("$@B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\|()1{}[]?-_+~<>i!lI;:,\"^`'. ")
```

下面是 RGB 值转字符的函数，注意 alpha 值为 0 的时候表示图片中该位置为空白：

```python
def get_char(r,g,b,alpha = 256):

    # 判断 alpha 值
    if alpha == 0:
        return ' '

    # 获取字符集的长度，这里为 70
    length = len(ascii_char)
    
    # 将 RGB 值转为灰度值 gray，灰度值范围为 0-255
    gray = int(0.2126 * r + 0.7152 * g + 0.0722 * b)

    # 灰度值范围为 0-255，而字符集只有 70
    # 需要进行如下处理才能将灰度值映射到指定的字符上
    unit = (256.0 + 1)/length
    
    # 返回灰度值对应的字符
    return ascii_char[int(gray/unit)]
```

### 处理图片

首先将 RGB 值转为灰度值，然后使用灰度值映射到字符列表中的某个字符。

下面是我们的字符画所使用的字符集，一共有 70 个字符，为了方便写入到实验环境中，可以使用实验环境右边工具栏上的[剪切板](https://www.shiyanlou.com/library/shiyanlou-docs/feature/clipboard.md)将以下代码内容拷贝到实验环境中，注意需要使用右键复制和拷贝，不要使用 `Ctrl-C/Ctrl-V` 快捷键。字符的种类与数量可以自己根据字符画的效果反复调试：

```python
ascii_char = list("$@B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\|()1{}[]?-_+~<>i!lI;:,\"^`'. ")
```

下面是 RGB 值转字符的函数，注意 alpha 值为 0 的时候表示图片中该位置为空白：

```python
def get_char(r,g,b,alpha = 256):

    # 判断 alpha 值
    if alpha == 0:
        return ' '

    # 获取字符集的长度，这里为 70
    length = len(ascii_char)
    
    # 将 RGB 值转为灰度值 gray，灰度值范围为 0-255
    gray = int(0.2126 * r + 0.7152 * g + 0.0722 * b)

    # 灰度值范围为 0-255，而字符集只有 70
    # 需要进行如下处理才能将灰度值映射到指定的字符上
    unit = (256.0 + 1)/length
    
    # 返回灰度值对应的字符
    return ascii_char[int(gray/unit)]
```

### 完整代码参考

下面是 `ascii.py` 的完整代码，供参考：

```python
# -*- coding=utf-8 -*-

from PIL import Image
import argparse

#命令行输入参数处理
parser = argparse.ArgumentParser()

parser.add_argument('file')     #输入文件
parser.add_argument('-o', '--output')   #输出文件
parser.add_argument('--width', type = int, default = 80) #输出字符画宽
parser.add_argument('--height', type = int, default = 80) #输出字符画高

#获取参数
args = parser.parse_args()

IMG = args.file
WIDTH = args.width
HEIGHT = args.height
OUTPUT = args.output

ascii_char = list("$@B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\|()1{}[]?-_+~<>i!lI;:,\"^`'. ")

# 将256灰度映射到70个字符上
def get_char(r,g,b,alpha = 256):
    if alpha == 0:
        return ' '
    length = len(ascii_char)
    gray = int(0.2126 * r + 0.7152 * g + 0.0722 * b)

    unit = (256.0 + 1)/length
    return ascii_char[int(gray/unit)]

if __name__ == '__main__':

    im = Image.open(IMG)
    im = im.resize((WIDTH,HEIGHT), Image.NEAREST)

    txt = ""

    for i in range(HEIGHT):
        for j in range(WIDTH):
            txt += get_char(*im.getpixel((j,i)))
        txt += '\n'

    print(txt)
    
    #字符画输出到文件
    if OUTPUT:
        with open(OUTPUT,'w') as f:
            f.write(txt)
    else:
        with open("output.txt",'w') as f:
            f.write(txt)
```