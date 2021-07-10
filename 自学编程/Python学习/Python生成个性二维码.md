# Python生成个性二维码

### 一、实验介绍

#### 1.1 实验内容

本课程通过调用MyQR接口来实现生成个人所需二维码，并可以设置二维码的大小、是否在现有图片的基础上生成、是否生成动态二维码。

本课程主要面向Python3初学者。

#### 1.2 知识点

- Python3基础
- MyQR库

#### 1.3 效果截图

1.3.1 **普通二维码**

![1-1.3-1](https://doc.shiyanlou.com/document-uid731737labid7103timestamp1530002363879.png)

1.3.2 **带图片的艺术二维码**

![1-1.3-2](https://doc.shiyanlou.com/document-uid731737labid7103timestamp1530002380367.png)

1.3.3 **动态二维码**

![1-1.3-3](https://doc.shiyanlou.com/document-uid731737labid7103timestamp1530002386054.png)

#### 1.4 实验环境

- python-3.5.2
- MyQR-2.3.1

### 二、实验准备

#### 2.1 创建环境

打开终端，进入 `Code` 目录，并将其作为我们的工作目录。

```
$ cd Code
```

#### 2.2 下载`MyQR`

```
$ sudo pip3 install MyQR
```

#### 2.3 下载所需资源文件并解压

```
Code/ $ wget http://labfile.oss.aliyuncs.com/courses/1126/Sources.zip  #这里提供制作二维码所需要的图片资源
Code/ $ unzip Sources.zip
```

#### 2.4 FreeImage

由于虚拟环境缺少了一些FreeImage依赖。我们在这里手动添加。

在`shiyanlou`根目录里打开终端:

```
shiyanlou/ $ mkdir .imageio && cd .imageio
.imageio/ $ mkdir freeimage && cd freeimage
freeimage/ $ wget http://labfile.oss.aliyuncs.com/courses/1126/libfreeimage-3.16.0-linux64.so
```

### 三、实验步骤

接下来，我们会自己制作普通二维码、带有图片的艺术二维码和动态二维码。

#### 3.1普通二维码

确保当前目录为`Code`,在命令行中输入 python3 ，进入 python3 环境：

```
Code/ $ python3
```

在 python3 环境中输入以下代码：

```
>>>from MyQR import myqr
>>>myqr.run('https://www.shiyanlou.com')
```

大功告成，那么来看一看自己制作的第一张二维码图片吧!

先退出python3环境

```python
>>>quit()
```

再使用火狐浏览器预览

```
Code/ $ firefox qrcode.png
```

效果图：

![1-3.1-1](https://doc.shiyanlou.com/document-uid731737labid7103timestamp1530003070325.png)

快快拿起手机扫一扫，看看是否有效，若成功，将跳转到实验楼主页。

下面我们来详细的讲解一下 `myqr.run()` 函数里面的参数

| 参数       | 含义           | 详细                                                         |
| ---------- | -------------- | ------------------------------------------------------------ |
| words      | 二维码指向链接 | str，输入链接或者句子作为参数                                |
| version    | 边长           | int，控制边长，范围是1到40，数字越大边长越大,默认边长是取决于你输入的信息的长度和使用的纠错等级 |
| level      | 纠错等级       | str，控制纠错水平，范围是L、M、Q、H，从左到右依次升高，默认纠错等级为'H' |
| picture    | 结合图片       | str，将QR二维码图像与一张同目录下的图片相结合，产生一张黑白图片 |
| colorized  | 颜色           | bool，使产生的图片由黑白变为彩色的                           |
| contrast   | 对比度         | float，调节图片的对比度，1.0 表示原始图片，更小的值表示更低对比度，更大反之。默认为1.0 |
| brightness | 亮度           | float，调节图片的亮度，其余用法和取值与 contrast 相同        |
| save_name  | 输出文件名     | str，默认输出文件名是"qrcode.png"                            |
| save_dir   | 存储位置       | str，默认存储位置是当前目录                                  |

#### 3.2带图片的艺术二维码

光是二维码，是否太单调了呢？没关系，我们能加上我们想要的图片，使二维码更具辨识度！ 我们准备了实验楼的Logo：

实验楼Logo图片：

![1-3.2-1](https://doc.shiyanlou.com/document-uid731737labid7103timestamp1530003118726.png)

当然，Sources文件夹里有更多的图片，你也可以选择你个人喜爱的一张来制作艺术二维码！

让我们将这张图加入到我们的二维码中，加入过程需要在参数里指定实验楼Logo图片的地址，我们也要设置新图片的保存名，以免和上一张二维码图片冲突。

```python
>>>myqr.run(
...    words='https://www.shiyanlou.com',
...    picture='Sources/shiyanlouLogo.png',
...    save_name='artistic.png',
...)
```

再次退出python3环境

```python
>>>quit()
```

使用火狐浏览器打开图片

```
Code/ $ firefox artistic.png
```

黑白实验楼Logo二维码：

![1-3.2-2](https://doc.shiyanlou.com/document-uid731737labid7103timestamp1530003143304.png)

黑白的，似乎不是那么好看，彩色的如何呢？ 实现彩色也非常简单，在参数里将 `colorized` 参数值设为 `True`。

```python
>>>myqr.run(
...    words='https://www.shiyanlou.com',
...    picture='Sources/shiyanlouLogo.png',
...    colorized=True,
...    save_name='artistic_Color.png',
...)
```

打开图片

```
Code/ $ firefox artistic_Color.png
```

彩色实验楼Logo二维码：

![1-3.2-3](https://doc.shiyanlou.com/document-uid731737labid7103timestamp1530003154555.png)

好看多了，但我们的实验并没有到此为止哦！

#### 3.3动态二维码

其实生成动态二维码，并没有想象的那么复杂。 在这里，我们使用美丽的新垣结衣GIF！

新垣结衣GIF:

![1-3.3-1](https://doc.shiyanlou.com/document-uid731737labid7103timestamp1530003207033.png)

在生成动态二维码的过程中，值得注意的一点是，我们生成保存的文件也必须是` .gif` 格式哟。 让我们赶快开始！

```python
>>>myqr.run(
...    words='https://www.shiyanlou.com',
...    picture='Sources/gakki.gif',
...    colorized=True,
...    save_name='Animated.gif',
...)
```

新鲜出炉的动图，新垣结衣动态二维码：

![1-3.3-2](https://doc.shiyanlou.com/document-uid731737labid7103timestamp1530003221452.png)

效果很不错呢，拿起手机试着扫扫看。

### 四、MyQR源码解读

MyQR源码来自于github上的[sylnsfar/qrcode](https://github.com/sylnsfar/qrcode)项目，大家可以通过克隆的方式下载源码来学习，可以使用如下命令行：

```
Code/ $ git clone https://github.com/sylnsfar/qrcode.git
```

如果下载速度较慢的话，也可以下载我们服务器上面的源码，可以通过如下命令：

```
Code/ $ wget http://labfile.oss.aliyuncs.com/courses/1126/qrcode-master.zip
Code/ $ unzip qrcode-master.zip
```

下面我们将一起来读下MyQR的源码内容，并且针对重点部分给大家详细讲解。

#### 1.MyQR文件结构

```
qrcode
│   LICENSE.md  
│   README.md    
│   requirements.txt    #环境依赖文件
|   myqr.py
|
└───MyQR
│   │   __init__.py
│   │   myqr.py     #调用的文件
│   │   terminal.py #设置参数
|   |
│   └───mylibs
│       │   __init__.pt
│       │   constant.py  #数据分析
|       |   data.py     #数据编码
│       │   ECC.py      #纠错编码，Error Correction Codewords 
|       |   structure.py    #数据结构
|       |   matrix.py       #获得QR矩阵
|       |   draw.py         #生成二维码
|       |   theqrmodule.py  #结合函数
│   
└───example
    │   0.png
    │   1.png
    |   2.png
    |   ...
```

大家可以执行如下命令查看整个文件的目录树：

```
tree qrcode-master
```

#### 2.生成二维码的步骤

2.1 数据分析`MyQR/mylibs/constant.py`

确定编码的字符类型，按相应的字符集转换成符号字符。

2.2 数据编码`MyQR/mylibs/data.py`

将数据字符转换为位流，每8位一个码字，整体构成一个数据的码字序列。

2.3 纠错编码`MyQR/mylibs/ECC.py`

按需要将上面的码字序列分块，并根据纠错等级和分块的码字，产生纠错码字，并把纠错码字加入到数据码字序列后面，成为一个新的序列。

2.4 构造最终数据信息`MyQR/mylibs/structure.py + matrix.py`

在规格确定的条件下，将上面产生的序列按次序放入分块中，将数据转成能够画出二维码的矩阵。

创建二维码的矩阵

```python
# MyQR/mylibs/matrix.py
def get_qrmatrix(ver, ecl, bits):
    num = (ver - 1) * 4 + 21
    qrmatrix = [[None] * num for i in range(num)]
    # 添加查找器模式和添加分隔符
    add_finder_and_separator(qrmatrix)

    # 添加校准模式
    add_alignment(ver, qrmatrix)

    # 添加时间模式
    add_timing(qrmatrix)
    
    # 添加涂黑模块和保留区域
    add_dark_and_reserving(ver, qrmatrix)
    
    maskmatrix = [i[:] for i in qrmatrix]
    
    # 放置数据位
    place_bits(bits, qrmatrix)
    
    # 蒙版操作
    mask_num, qrmatrix = mask(maskmatrix, qrmatrix)
    
    # 格式信息
    add_format_and_version_string(ver, ecl, mask_num, qrmatrix)

    return qrmatrix
```

2.5 生成二维码`MyQR/mylibs/draw.py`

使用 `draw.py` 画出二维码。

```python
def draw_qrcode(abspath, qrmatrix):
    unit_len = 3
    x = y = 4*unit_len
    pic = Image.new('1', [(len(qrmatrix)+8)*unit_len]*2, 'white')   #新建一张白色的底图
    
    '''
    循环矩阵中的单位，在需要涂黑的单位启用dra_a_black_unit()函数涂黑。
    '''
    for line in qrmatrix:
        for module in line:
            if module:
                draw_a_black_unit(pic, x, y, unit_len)  #画出黑单位
            x += unit_len
        x, y = 4*unit_len, y+unit_len

    saving = os.path.join(abspath, 'qrcode.png')
    pic.save(saving)    # 保存二维码图片
    return saving
```

#### 3.合并图片的原理

让我们来看一下 `/MyQR/myqr.py` 中的 `combine()` 方法,此方法调用了 `Pillow` 库

读取图片操作

```python
    qr = Image.open(qr_name)    #读取二维码图片
    qr = qr.convert('RGBA') if colorized else qr    #判断二维码是否有色
        
    bg0 = Image.open(bg_name).convert('RGBA')   #读取要合并的图片
    bg0 = ImageEnhance.Contrast(bg0).enhance(contrast)  # 调节对比度
    bg0 = ImageEnhance.Brightness(bg0).enhance(brightness)  # 调节亮度
```

将新加的图片覆盖原有的二维码图片，生成新的图片并保存。

```python
    for i in range(qr.size[0]-24):
        for j in range(qr.size[1]-24):
            if not ((i in (18,19,20)) or (j in (18,19,20)) or (i<24 and j<24) or (i<24 and j>qr.size[1]-49) or (i>qr.size[0]-49 and j<24) or ((i,j) in aligs) or (i%3==1 and j%3==1) or (bg0.getpixel((i,j))[3]==0)):
                qr.putpixel((i+12,j+12), bg.getpixel((i,j)))
```

源码简单的解读就是这些，如果想更深入的了解，请直接[点击此处](https://github.com/sylnsfar/qrcode)亲自阅读源码。

### 五、实验总结

二维码的内容，就到此结束了。二维码在日常生活中的使用场景很多，大家可以结合实际生活来使用。

本实验主要的知识点如下：

- 调用MyQR库
- 了解MyQR库的具体实现原理

#### 参考资料

- [artistic QR Code in Python （Animated GIF qr code）- Python 艺术二维码生成器 （GIF动态二维码、图片二维码）](https://github.com/sylnsfar/qrcode)
- [QR Code Tutorial](https://www.thonky.com/qr-code-tutorial/)
- [二维码（QR code）基本结构及生成原理](https://blog.csdn.net/u012611878/article/details/53167009)
- [Pillow](https://pillow.readthedocs.io/en/5.1.x/index.html)

#### 版权声明

GPLv3

