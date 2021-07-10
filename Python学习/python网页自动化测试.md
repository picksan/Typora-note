Selenium自动化测试

### 安装的库

```
sudo pip3 install --upgrade pip
sudo pip3 install selenium
```

### 实验环境

- Firefox 浏览器
- python 3.5
- geckodriver 0.22.0
- selenium 3

### selenium介绍

当我们进入[selenium 官网](https://www.seleniumhq.org/)时可以看到，网站的 title 上写的是 `Selenium - Web Browser Automation`，翻译过来就是网站浏览器自动化。也就是说我们把平时在网页上做的功能测试用 Selenium 代码实现，这样在回归测试的时候就可以达到省时省力的目的。

Selenium 在工作中的应用常见于功能基本稳定、没有频繁大变动的网页。所以我们一般是在业务功能上线以后，为确保页面稳定，用 Selenium 实现自动化回归测试，结合 git、Jenkins 一起，每当有新功能上线时都会执行写好的 Selenium 代码以验证新上线的业务对原有页面功能没有造成影响。如有报错，则发送相应的通知，这样就可以确保对线上功能出现的未预期 bug 进行及时的修复。

### 浏览器和驱动

下载firefox的浏览器驱动

```
$ wget https://labfile.oss.aliyuncs.com/courses/1163/geckodriver-v0.22.0-linux64.tar.gz
```

将文件解压，并移动至`/usr/local/bin`文件夹中。

```
$ tar zxvf geckodriver-v0.22.0-linux64.tar.gz
$ sudo mv geckodriver /usr/local/bin
```

将目录切换至桌面：

```
$ cd /home/shiyanlou/Desktop
```

下面我们来验证是否正常安装，在终端使用命令`vim demo.py`创建文件并写入代码：

```
#! /usr/bin/python3

from selenium import webdriver

driver = webdriver.Firefox()
driver.get("https://www.lanqiao.cn")
```

输入`python3 demo.py`如果浏览器打开并进入我们的网站，则环境配置就成功了

### 浏览器操作

在终端使用命令`vim demo2.py`创建文件并写入代码:

```
#! /usr/bin/python3

from selenium import webdriver
from time import sleep


driver = webdriver.Firefox()

# 浏览器进入百度网站
driver.get("https://www.baidu.com")

# 设置浏览器宽800，高400
driver.set_window_size(800, 400)

# 等待3秒
sleep(3)

# 刷新页面
driver.refresh()

# 等待3秒
sleep(3)

# 最大化窗口
driver.maximize_window()

# 退出浏览器
driver.quit()
```

以上代码会在浏览器中执行：

- 打开浏览器
- 进入百度网站
- 设置窗口大小为宽 800，高 400
- 等待 3 秒
- 刷新页面
- 最大化窗口
- 退出浏览器

> `说明:`由于环境限制，当前浏览器不能实现后退操作，所以如果大家在本地搭建环境，可以增加：
>
> - 倒退页面
> - 前进页面

示例代码

```
#! /usr/bin/python3

from selenium import webdriver
from time import sleep


driver = webdriver.Firefox()

# 浏览器进入百度网站
driver.get("https://www.baidu.com")

# 设置浏览器宽800，高400
driver.set_window_size(800, 400)

# 等待3秒
sleep(3)

# 最大化窗口
driver.maximize_window()
# 进入另一个网站
driver.get("https://www.lanqiao.cn/")
sleep(3)

# 后退到上一个页面--百度网站
driver.back()

sleep(3)

# 前进到下一个页面--实验楼网站
driver.forward()

sleep(3)

# 退出浏览器
driver.quit()
```

### 定位元素

webdriver 提供了一系列的元素定位方法，常用的有以下几种:

- id
- name
- class name
- tag name
- link text
- partial link text
- xpath
- css selector

分别对应 python webdriver 中的方法为：

- find_element_by_id()
- find_element_by_name()
- find_element_by_class_name()
- find_element_by_tag_name()
- find_element_by_link_text()
- find_element_by_partial_link_text()
- find_element_by_xpath()
- find_element_by_css_selector()

#### 点击定位到的元素

- click()

> 使用方法：一般为先进行元素的定位，如果该元素可以点击如：超链接、文本框、带有超链接的图片等，则该元素可以进行点击操作：`find_element_by_xxx().click()`。

#### 清空文本框，向文本框输入内容

- 清空：`clear()`
- 输入：`.send_keys("输入的内容")`

> 使用方法：无论是清空还是输入操作，都是先进行元素即文本框的定位，然后调用对应的方法，即：清空`find_element_by_xx().clear()`，输入`find_element_by_xx().send_keys("输入的内容")`

#### 获取元素属性

- 文本信息：`.text`

> 使用方法：定位到对应元素以后，直接调用方法：`find_element_by_xx().text`，注意：text 后面不要加括号

- 元素尺寸：`.size`

> 使用方法：同上

- 其他属性：`.get_attribute("想获取的属性名")`

> 使用方法：定位到对应元素以后，调用`.get_attribute("属性名")`方法，传值为想获取的属性名。注：查看元素的各个属性可通过 Chrome 自带的`开发者工具`，快捷键为`F12`，通过`元素查看器`定位到想查看的元素，然后在开发者工具中查看具体的属性名，如`class`、`type`、`id`等。

这里我们使用[51Testing 软件测试论坛](http://bbs.51testing.com/forum.php)作为演示网站，如果大家没有账号需要先去注册一个，下面的代码将会使用到账号信息，在终端使用命令`vim demo3.py`创建文件并写入代码：

```
#! /usr/bin/python3

from selenium import webdriver
from time import sleep


driver = webdriver.Firefox()

# 进入51testing网站
driver.get("http://bbs.51testing.com/forum.php")
sleep(3)

# 用id定位账号输入框并输入账号
driver.find_element_by_id("ls_username").send_keys("您的用户名")

# 用id定位密码输入框并输入密码
driver.find_element_by_id("ls_password").send_keys("密码")

# 定位“登录”按钮并获取登录按钮的文本
txt = driver.find_element_by_xpath('//*[@id="lsform"]/div/div[1]/table/tbody/tr[2]/td[3]/button').text

# 打印获取的文本
print(txt)

# 定位“登录”按钮并获取登录按钮的type属性值
type = driver.find_element_by_xpath('//*[@id="lsform"]/div/div[1]/table/tbody/tr[2]/td[3]/button').get_attribute("type")

# 打印type属性值
print(type)

# 定位“登录”按钮并进行点击操作
driver.find_element_by_xpath('//*[@id="lsform"]/div/div[1]/table/tbody/tr[2]/td[3]/button').click()
```

在终端执行`python3 demo3.py`运行，结果显示如下：

### 下拉页面

说明：下拉页面需要用 js 命令

- 下拉指定高度

```
js = 'document.documentElement.scrollTop=具体的下拉高度值;'
driver.execute_script(js)
```

> 解释：`js = 'document.documentElement.scrollTop=具体的下拉高度值;'`为 js 语句，意为下拉页面滚动条；`driver.execute_script(js)`为 python 代码，意为执行上面的 js 语句。

- 用目标元素做参考下拉页面

```
target_element = driver.find_element_by_xx()

js = 'arguments[0].scrollIntoView();'

driver.execute_script(js,target_element)
```

> 解释：`target_element = driver.find_element_by_xx()`先对目标元素进行定位；`js = 'arguments[0].scrollIntoView();'`js 下拉命令；`driver.execute_script(js, target_element)`python 代码，执行脚本，传两个参数，第一个是 js 命令，第二个是目标元素。

在终端使用命令`vim demo4.py`创建文件并写入代码：

```
#! /usr/bin/python3

from selenium import webdriver
from time import sleep

driver = webdriver.Firefox()
driver.get("http://bbs.51testing.com/forum.php")
sleep(3)

# 页面下拉指定高度
js = 'document.documentElement.scrollTop=800;'
driver.execute_script(js)
```

在终端执行`python3 demo4.py`运行，页面在等待 3 秒后会出现下拉行为。

### 页面弹窗alert的定位

如果页面有`alert`形式的提醒框，则用以下语句

- driver.switch_to.alert

```
alert = driver.switch_to.alert

# 查看alert中的文字

print(alert.text)

# 点击确定

alert.accept()

# 点击取消（如果有）

alert.dismiss()
```

### 切换窗口

- .switch_to.window()

> 说明：很多时候我们点击按钮以后会新开页面，这时候要根据页面的`句柄`来切换窗口，获取所有页面句柄方法为`.window_handles`，而获取当前页面的句柄语法则为`.current_window_handle`，现在我们假设页面开了两个窗口，那么如何在两个窗口之间进行切换呢？很简单，就是用一个`for`循环即可，如果循环到的句柄与当前句柄不一致，那么就切换句柄：

```
# 获取窗口所有句柄
all_handles = driver.window_handles
# 获取当前窗口句柄
curr_window = driver.current_window_handle
# 遍历所有句柄
for k in all_handles:
    # 如果不是当前窗口句柄
    if k != curr_window:
        # 窗口句柄切换
        driver.switch_to.window(k)
```

### 定位iframe

- .switch_to.frame():切换到 iframe
- .switch_to.default_content(): 切换出 iframe

> 说明：iframe 经常在账号、密码输入框、发帖内容编辑框处出现，一般我们需要先通过开发人员工具确定该输入框是否是 iframe，如果是，则需要先定位 iframe。对 iframe 定位，一般需要先通过 xpath 定位到 iframe 的位置，然后通过`.switch_to.frame()`方法切换到 iframe 中，iframe 就像一个盒子，我们进入了盒子内部，进行预期的操作，然后需要跳出盒子才能继续对页面元素进行操作，所以执行完 iframe 内的操作后需要跳出 iframe 可以通过`.switch_to.default_content()`方法。

```
iframe = driver.find_element_by_xpath()

# 切换到iframe

driver.switch_to.frame(iframe)

...页面操作代码...

# 跳出iframe

driver.switch_to.default_content()
```

### 实验总结

本次实验我们介绍了 Selenium 的用途，以及环境配置、Selenium 基本语法，为后续完成 Selenium 代码做好了准备工作。

#### 参考链接

- [Selenium 官网](https://www.seleniumhq.org/)
- [geckodriver 下载链接](https://github.com/mozilla/geckodriver/releases)