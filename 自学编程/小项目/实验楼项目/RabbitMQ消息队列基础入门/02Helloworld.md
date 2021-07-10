# Hello world

### 实验简介

本节实验将通过一个程序发送 “Hello world”，另一个程序接受消息并且打印到屏幕上。

我们设计是这样的：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid59274labid2087timestamp1472375365539.png)

生产者（Producer）把消息发送到一个名为 “hello” 的队列中。消费者（Consumer）从这个队列中获取消息。

#### 知识点

- RabbitMQ 原理介绍
- 安装 RabbitMQ 库
- 简单的消息发送与接收

### 参考代码

```bash
cd /home/shiyanlou/

# 下载
wget https://labfile.oss.aliyuncs.com/courses/630/Code.zip

# 解压
unzip Code.zip
```

下载并解压后，本小节的参考代码在 `/home/shiyanlou/Code/2/` 中，你编写的代码需要存放在 `/home/shiyanlou/Code/` 中。

### 原理介绍

RabbitMQ 是一个消息代理。它的核心原理非常简单：接收和发送消息。你可以把它想像成一个邮局：你把信件放入邮箱，邮递员就会把信件投递到你的收件人处。在这个比喻中，RabbitMQ 就扮演着邮箱、邮局以及邮递员的角色。

RabbitMQ 和邮局的主要区别是，它不是用来处理纸张的，它是用来接收、存储和发送消息（message）这种二进制数据的。

在这里我们使用生产者、消费者模型来进行此次的模拟。

> 生产者消费者问题（英语：Producer-consumer problem），也称有限缓冲问题（英语：Bounded-buffer problem），是一个多线程同步问题的经典案例。该问题描述了两个共享固定大小缓冲区的线程——即所谓的“生产者”和“消费者”——在实际运行时会发生的问题。生产者的主要作用是生成一定量的数据放到缓冲区中，然后重复此过程。与此同时，消费者也在缓冲区消耗这些数据。该问题的关键就是要保证生产者不会在缓冲区满时加入数据，消费者也不会在缓冲区中空时消耗数据。

- 生产者（Producer）

生产者生成一定量的数据放到缓冲区中的程序统称，在本实验环境中，产生消息并发送到消息队列中的程序就是一个 [生产者](https://zh.wikipedia.org/wiki/生产者消费者问题)(producer)。也是我们上文比喻中写信，投信的人。

我们一般用 “P” 来表示:

![pic](https://doc.shiyanlou.com/document-uid59274labid2087timestamp1472375625848.png)

- 队列（Queue）

它是一种特殊的线性表，在本实验环境中用于存放消息，也就是上文比喻中的邮箱。

消息通过你的应用程序和 RabbitMQ 进行传输，它们能够只存储在一个队列（queue）中。 队列（queue）没有任何限制，你要存储多少消息都可以——基本上是一个无限的缓冲。多个生产者（producers）能够把消息发送给同一个队列，同样，多个消费者（consumers）也能够从同一个队列（queue）中获取数据。

队列可以绘制成这样（图上是队列的名称）：

![pic](https://doc.shiyanlou.com/document-uid59274labid2087timestamp1472375632829.png)

- 消费者（Consumer）

消费者便是从消息队列中取出数据的程序统称。也就是上文比喻中的收件人，在本实验中一个[消费者](http://blog.csdn.net/morewindows/article/details/7577591)（consumer）就是一个等待获取消息的程序。

我们把它绘制为 "C"：

![pic](https://doc.shiyanlou.com/document-uid59274labid2087timestamp1472375649617.png)

需要指出的是生产者、消费者、代理一般不会放置在在同一个设备上；事实上大多数应用也确实不在会将他们放在一台机器上。

### 安装 RabbitMQ 库

RabbitMQ 使用的是 AMQP 协议。要使用 rabbitmq，你需要一个库来解读这个协议。几乎所有的编程语言都有可选择的库。python 也是一样，可以从以下几个库中选择，他们都可以实现 python 与 rabbitmq 的对接：

- [py-amqplib](http://barryp.org/software/py-amqplib/)
- [txAMQP](https://launchpad.net/txamqp)
- [pika](http://github.com/pika/pika)

这次实验我们用 pika 来做演示，通过 pip 来安装：

```bash
# 更新软件包列表
sudo apt-get update

# 安装所需要的依赖
sudo apt-get install -y python-pip git-core

# 更新 pip
sudo pip install --upgrade pip

# 安装 pika
sudo pip3 install pika
```

![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190905-1567653701242)

### 发送消息

![pic](https://doc.shiyanlou.com/document-uid59274labid2087timestamp1472375679504.png)

我们会写一个这样的程序 send.py 用来发送一个消息到队列中。请注意将代码存放在 `/home/shiyanlou/Code/`，否则将无法通过检测。

#### 创建连接

首先要做的事情就是建立一个到 RabbitMQ 服务器的连接。

```python
#!/usr/bin/env python3
import pika

connection = pika.BlockingConnection(
              pika.ConnectionParameters('localhost'))
channel = connection.channel()
```

#### 创建队列

现在我们已经连接上服务器了，那么，在发送消息之前我们需要确认队列是存在的。如果我们把消息发送到一个不存在的队列，RabbitMQ 会丢弃这条消息。我们先创建一个名为 hello 的队列，然后把消息发送到这个队列中。

```python
channel.queue_declare(queue='hello')
```

- 在 RabbitMQ 中，消息是不能直接发送到队列，它需要发送到交换机（exchange）中，它使用一个空字符串来标识。交换机允许我们指定某条消息需要投递到哪个队列。 routing_key 参数必须指定为队列的名称：

```python
channel.basic_publish(exchange='',
                      routing_key='hello',
                      body='Hello World!')
print(" [x] Sent 'Hello World!'")
```

#### 关闭连接

在退出程序之前，我们需要确认网络缓冲已经被刷写、消息已经投递到 RabbitMQ，然后就关闭连接。

```python
connection.close()
```

#### 完整的代码

代码介绍到此结束，完成源代码文件编写 `/home/shiyanlou/Code/send.py`。

```python
#!/usr/bin/env python3
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(
        host='localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

channel.basic_publish(exchange='',
                      routing_key='hello',
                      body='Hello World!')
print(" [x] Sent 'Hello World!'")
connection.close()
```

### 获取数据

![pic](https://doc.shiyanlou.com/document-uid59274labid2087timestamp1473061083975.png)

我们的第二个程序 `receive.py`，将会从队列中获取消息并打印消息。

#### 创建连接

同样我们还是先要连接到 RabbitMQ 服务器。连接服务器的代码和之前是一样的。

下一步也和之前一样，我们需要确认队列是存在的。**使用 queue_declare 创建一个队列——我们可以运行这个命令很多次，但是只有一个队列会被创建。**

```python
channel.queue_declare(queue='hello')
```

> 为什么要重复声明队列呢 —— 我们已经在前面的代码中声明过它了。如果我们确定了队列是已经存在的，那么我们可以不这么做，比如此前预先运行了 send.py 程序。可是我们并不确定哪个程序会首先运行。这种情况下，在程序中重复将队列重复声明一下是种值得推荐的做法.

#### 列出所有的队列

列出所有的队列:你也许希望查看 RabbitMQ 中有哪些队列、有多少消息在队列中。此时你可以使用 `rabbitmqctl` 工具（使用有权限的用户）：

```bash
# 先确保服务已经开启
sudo service rabbitmq-server start

python3 send.py

sudo rabbitmqctl list_queues
```

![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190905-1567661388691)

#### 创建回调函数

从队列中获取消息相对来说稍显复杂。需要为队列定义一个回调（callback）函数。当我们获取到消息的时候，Pika 库就会调用此回调函数。这个回调函数会将接收到的消息内容输出到屏幕上。

```python
def callback(ch, method, properties, body):
    print(" [x] Received %r" % body)
```

下一步，我们需要告诉 RabbitMQ 这个回调函数将会从名为 "hello" 的队列中接收消息：

```python
channel.basic_consume(queue='hello',
                      auto_ack=True,
                      on_message_callback=callback)
```

要成功运行这些命令，我们必须保证队列是存在的，我们的确可以确保它的存在——因为我们之前已经使用 queue_declare 将其声明过了。auto_ack 参数下节会进行介绍。

最后，我们输入一个用来等待消息数据并且在需要的时候运行回调函数的无限循环。

```python
print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()
```

#### 完整的代码

代码介绍到此结束，完成源代码文件编写 `/home/shiyanlou/Code/receive.py`。

```python
#!/usr/bin/env python3
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(
        host='localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

def callback(ch, method, properties, body):
    print(" [x] Received %r" % body)

channel.basic_consume(queue='hello',
                      auto_ack=True,
                      on_message_callback=callback)

print(' [*] Waiting for messages. To exit press CTRL+C')

channel.start_consuming()
```

### 运行

现在就可以在终端中运行我们的程序了。

注意要先启动服务：

```bash
$ sudo service rabbitmq-server start
```

然后，用 send.py 发送一条消息：

```bash
python3 send.py
```

生产者（producer）程序 send.py 每次运行之后就会停止。现在我们就来接收消息：

```bash
python3 receive.py
```

![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190905-1567660756623)

成功了！我们已经通过 RabbitMQ 发送第一条消息。你也许已经注意到了，receive.py 程序并没有退出。它一直在准备获取消息，你可以通过 `Ctrl + C` 来中止它。

### 实验总结

我们已经学会如何发送消息到一个已知队列中并接收消息。是时候移步到第二部分了，我们将会建立一个简单的工作队列（Work Queue）。

#### 参考资料

- [http://rabbitmq.mr-ping.com](http://rabbitmq.mr-ping.com/description.html)
- [RabbitMQ 官方文档](http://www.rabbitmq.com/)