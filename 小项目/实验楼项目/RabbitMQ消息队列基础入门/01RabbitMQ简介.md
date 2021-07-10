# 实验一RabbitMQ简介

[RabbitMQ 消息队列基础入门_Linux - 蓝桥云课 (lanqiao.cn)](https://www.lanqiao.cn/courses/630)

### 实验简介

本课程为《RabbitMQ 消息队列》的第一课，主要介绍 RabbitMQ 的功能、历史，并且讲述它的安装教程，让大家对 RabbitMQ 有一个初步的认识。

#### 课程说明

本课程用 python 来应用 RabbitMQ。

#### 知识点

- RabbitMQ 功能简介
- RabbitMQ 支持的平台和语言
- RabbitMQ 服务器安装
- RabbitMQ 的基本操作

### RabbitMQ 消息队列简介

RabbitMQ 是高级消息队列协议（AMQP）的开源消息代理软件。

RabbitMQ 服务器是用 Erlang 语言编写的，消息系统允许软件、应用相互连接和扩展。这些应用可以相互链接起来组成一个更大的应用，或者将用户设备和数据进行连接。消息系统通过将消息的发送和接收分离来实现应用程序的异步和解耦。

或许你正在考虑进行数据投递，非阻塞操作或推送通知。或许你想要实现发布 / 订阅，异步处理，或者工作队列。所有这些都可以通过消息实现。RabbitMQ 是一个消息代理 - 一个消息系统的媒介。它可以为你的应用提供一个通用的消息发送和接收平台，并且保证消息在传输过程中的安全。

**功能亮点：**

- 可靠性：RabbitMQ 提供了各种功能，让你权衡性能与可靠性，其中包括持久性，交付确认和高可用性。
- 灵活的路由：消息在到达队列之前，通过交换机的路由。RabbitMQ 为典型的路由逻辑提供了几个内置的交换机类型。对于更复杂的路由，则可以绑定几种交换机一起使用甚至可以自己实现交换机类型，并且把它作为一个插件的来使用。
- 集群：在本地网络上的几个 RabbitMQ 服务器可以聚集在一起，作为一个独立的逻辑代理来使用。
- 联合：对于服务器来说，它比集群需要更多的松散和非可靠链接。为此 RabbitMQ 提供了联合模型。
- 高度可用队列：在群集中，队列可以被镜像到几个机器中，确保您的消息即使在出现硬件故障的安全。
- 多协议：RabbitMQ 支持上各种消息传递协议的消息传送.
- 许多客户端：有你能想到的几乎任何语言 RabbitMQ 客户端。
- 管理用户界面：RabbitMQ 附带一个简单使用管理用户界面，允许您监视和控制您的消息代理的各个方面。
- 追踪：如果您的消息系统行为异常，RabbitMQ 提供跟踪支持，让你找出问题是什么。
- 插件系统：RabbitMQ 附带各种插件扩展，并且你也可以写你自己插件.
- 商业支持:提供商业支持、 培训和咨询。
- 大型社区:有一个庞大的社区 RabbitMQ，有各种各样的客户端、 插件、 指南等。

### 安装到 Debian / Ubuntu

自 Debian since 6.0 (squeeze) 和 Ubuntu 9.04 之后，`rabbitmq-server` 就在 Ubuntu 的官方源里面了，可以直接使用 `apt` 进行安装。

如果你想安装最新版本，可以在 [官网](http://www.rabbitmq.com/download.html)下载，或者使用官方提供的源来安装。本次实验我们就用 Ubuntu 源里的版本安装，所有的依赖都可以被自动安装。

```bash
# 更新软件包列表
$ sudo apt-get update

# 安装 RabbitMQ Server
$ sudo apt-get install -y rabbitmq-server
```

### 管理服务器

当 RabbitMQ 安装完毕的时候服务器就会像后台程序一般运行起来了。作为一个管理员，可以像平常一样在 Debian 中使用以下命令启动和关闭服务

服务器相关命令：

- 启动

```bash
sudo service rabbitmq-server start
```

- 关闭

```bash
sudo service rabbitmq-server stop
```

- 查看状态

```bash
sudo service rabbitmq-server status
```

首先启动服务器：

![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190905-1567648236999)

然后查看状态：

![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190905-1567648099325)

### 控制系统限制

如果要调整系统限制（尤其是打开文件的句柄的数量）的话，可以通过编辑 `/etc/default/rabbitmq-server` 文件让服务启动的时候调用 ulimit。

例如，设置此服务打开文件句柄的最大数量为 1024 个（这也是默认设置）：

```txt
ulimit -n 1024
```

![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190905-1567648517721)

**句柄**：在程序设计中，句柄（handle）是一种特殊的智能指针。当一个应用程序要引用其他系统（如数据库、操作系统）所管理的内存块或对象时，就要使用句柄。

句柄与普通指针的区别在于，指针包含的是引用对象的内存地址，而句柄则是由系统所管理的引用标识，该标识可以被系统重新定位到一个内存地址上。这种间接访问对象的模式增强了系统对引用对象的控制。通俗的说就是我们调用句柄就是调用句柄所提供的服务，即句柄已经把它能做的操作都设定好了，我们只能在句柄所提供的操作范围内进行操作，但是普通指针的操作却多种多样，不受限制。

### 日志

服务器的输出被发送到 `RABBITMQ_LOG_BASE` 目录的 `RABBITMQ_NODENAME.log` 文件中。一些额外的信息会被写入到 `RABBITMQ_NODENAME-sasl.log` 文件中。

代理总是会把新的信息添加到日志文件尾部，所以完整的日志历史可以被保存下来。

你可以使用 logrotate 程序来执行必要的循环和压缩工作，并且你还可以更改它。默认情况下，脚本会每周执行一次对这些位于 `/var/log/rabbitmq/` 文件夹中的日志的处理。

你可以查看 `/etc/logrotate.d/rabbitmq-server` 来对 logrotate 进行配置。

![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190905-1567648719942)

查看日志的内容可以使用如下的方式：

```txt
# servername 是指你的主机名
less /var/log/rabbitmq/rabbit@servername.log
```

![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190905-1567649007149)

比如我这里主机名为 `9c908f4f0793`，因此文件名为 `rabbit@9c908f4f0793.log`（如上图）。

日志文件对应的内容为：

![图片描述](https://doc.shiyanlou.com/courses/uid977658-20190905-1567649116210)

### 支持的平台及语言

RabbitMQ 有着运行在所有 Erlang 所支持的平台之上的潜力，从嵌入式系统到多核心集群还有基于云端的服务器。

> 以下的平台是 Erlang 语言所支持的，因此 RabbitMQ 可以运行其上：

- Solaris
- BSD
- Linux
- MacOSX
- TRU64
- Windows NT/2000/XP/Vista/Windows 7/Windows 8
- Windows Server 2003/2008/2012
- Windows 95, 98
- VxWorks

> RabbitMQ 的开源版本通常被部署在以下的平台上：

- Ubuntu 和其他基于 Debian 的 Linux 发行版
- Fedora 和其他基于 RPM 包管理方式的 Linux 发行版
- openSUSE 和衍生的发行版（包括 SLES 和 SLERT）
- Mac OS X
- Windows XP 和 后续版本

> RabbitMQ 支持下列编程语言：

- C# (using .net/c# client)
- clojure (using Langohr)
- erlang (using erlang client)
- java (using java client)
- javascript/node.js (using amqp.node)
- perl (using Net::RabbitFoot)
- python (using pika)
- python-puka (using puka)
- ruby (using Bunny)
- ruby (using amqp gem)

### 实验总结

本节课主要介绍了 RabbitMQ 的功能，基本的安装、运行操作，以及介绍了它支持的平台和语言。RabbitMQ 基本涵盖了现在的主流平台。下节课将通过一个例子来介绍 RabbitMQ 操作。

#### 参考资料

- [句柄](https://zh.wikipedia.org/wiki/句柄)
- [http://rabbitmq.mr-ping.com](http://rabbitmq.mr-ping.com/description.html)
- [RabbitMQ 官方文档](http://www.rabbitmq.com/)
- [RabbitMQ wiki](https://zh.wikipedia.org/wiki/RabbitMQ)