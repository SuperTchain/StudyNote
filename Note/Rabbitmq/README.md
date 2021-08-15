[TOC]

# 1.RabbitMQ由浅入深入门全总结

## 1.1 什么是中间件？

> IDC（互联网数据中心）的定义：中间件是一种独立的系统软件服务程序，分布式应用软件借助这种软件在不同的技术之间共享资源，中间件位于客户机服务器的操作系统之上，管理计算资源和网络通信。

首先，**中间件是某一类软件的总称，而不是某一种具体的软件**。它是一种位于**平台（操作系统硬件） 和 应用程序之间**的通用服务，它屏蔽了底层操作系统的各种复杂性，减轻了开发人员的技术负担，同时它的设计不针对某一具体目标，而是提供具有普遍通用特点的功能模块服务，这些服务具有标准的程序接口和协议，根据平台的不同，也可以有不同的实现。

通俗的例子（仅供参考，并不算完全一致）：

- 我开了一家咖啡店，我身边有 A B C 等 n 家咖啡豆的供应商，但是我肯定要挑选价格又实惠，质量还不错的豆子，但是市场是受到多方面因素波动的，可能我现在的选择，在一段时间后已经不是最佳选项了。所以我专门找到一家市场中介，让他帮我操心这一摊子事情，我只和你说清价格和质量要求，你去找就是了，过程我一点也不操心。这个中介的概念，就类似中间件的

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3159ae3ab7f64c1ca0d0310498bd9516~tplv-k3u1fbpfcp-zoom-1.image)

### 1.1.1 分布式的概念（补充）

这一段，来自我之前写的 Dubbo 入门的那篇文章哈

在百度以及维基中的定义都相对专业且晦涩，大部分博客或者教程经常会使用《分布式系统原理和范型》中的定义，即：**“分布式系统是若干独立计算机的集合，这些计算机对于用户来说就像是单个相关系统”**

下面我们用一些篇幅来通俗的解释一下什么叫做分布式

#### 1.1.1.1 什么是集中式系统

提到分布式，不得不提的就是 **“集中式系统”**，这个概念最好理解了，它就是将功能，程序等安装在同一台设备上，就由这一台主机设备向外提供服务

举个最简单的例子：你拿一台PC主机，将其改装成了一台简单的服务器，配置好各种内容后，你将MySQL，Web服务器，FTP，Nginx 等等，全部安装在其中，打包部署项目后，就可以对外提供服务了，但是一旦这台机器无论是软件还是硬件出现了问题，整个系统都会受到严重的牵连错误，鸡蛋放在一个篮子里，要打就全打了

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/075d564f372545c9b225eb08ee476afc~tplv-k3u1fbpfcp-zoom-1.image)

#### 1.1.12 什么是分布式系统

既然集中式系统有这样一种牵一发而动全身的问题，那么分布式的其中一个作用，自然是来解决这样的问题了，正如定义中所知，分布式系统在用户的体验感官里，就像传统的单系统一样，一些变化都是这个系统本身内部进行的，对于用户并没有什么太大的感觉

例如：淘宝，京东这种大型电商平台，它们的主机都是数以万计的，否则根本没法处理大量的数据和请求，具体其中有什么划分，以及操作，我们下面会说到，但是对于用户的我们，我们不需要也不想关心这些，我们仍可以单纯的认为，我们面对的就是 “淘宝” 这一台 “主机”

所以分布式的一个相对专业一些的说法是这样的（进程粒度）**两个或者多个程序，分别运行在不同的主机进程上，它们互相配合协调，完成共同的功能，那么这几个程序之间构成的系统就可以叫做分布式系统**

- 这几者都是相同的程序 —— 分布式
- 这几者都是不同的程序 —— 集群

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/30795f3e2ece4f85b42ade558694addf~tplv-k3u1fbpfcp-zoom-1.image)

\## 1.2 什么是消息中间件/消息队列（MQ）

消息中间件，顾名思义就是用来处理消息相关服务的中间件，它提供了一种系统之间通信交互的通道，例如发送方只需要把想传输的信息交给消息中间件，而发送的协议，方式，发送过程中出现的网络，故障等等问题，都由中间件进行处理，因此它负责保证信息的可靠传输。

所以消息中间件，就是一种用来接受数据，存储数据，发送数据的技术，它提供了各种功能，可以实现消息的高可用，高可靠，也提供了很好的容错机制等。可以程序对系统资源的占用，以及传输效率的提升有很大帮助。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b6252497c80341aa9666b35a8ad31da4~tplv-k3u1fbpfcp-zoom-1.image)

\- 常说的 MQ 就是指消息队列，即 Message Quene，常见的消息队列有，经典的 ActivieMQ，热门的 Kafka，阿里的 RocketMQ 等等，以及这里讲解的 RabbitMQ。

- 不同的 MQ 有着不同的特点，以及其更加擅长的方向，倒也说不上谁好谁坏，只有谁更合适。

### 1.2.1 消息队列应用场景

根据业务的需要，其实它可以有多种应用场景，例如解耦，削峰填谷，广播等，我们举两个场景来梳理一下简单的过程

#### 1.2.1.1 业务解耦

最近在考虑买几本书看，就以买书下订单举例，当我点击购买之后，可能会有这么一串业务逻辑执行，① 减去库存容量 ② 生成订单 ③ 支付 ④ 更新订单状态 ⑤ 发送购买成功短信 ⑥ 更新商品快递揽收状态。在初期阶段，我们完全可以让这些业务同步执行，但是后期为了提升效率，就可以将需要立即执行的任务和可稍缓执行的任务进行分离，例如 ⑤ 发送购买成功短信 ⑥ 更新商品快递揽收状态，都可以考虑异执行。在主流程执行结束后，这些可稍缓的业务可以通过给 MQ 发送消息，就判定已经执行，保证流程先结束。然后再通过拉取 MQ 消息，或者 MQ 主动推送去异步执行其他的业务。

#### 1.2.1.2 削峰填谷

例如发送一条带有已读未读标识的公告信息，所以需要对每一个用户都写一条这样的公告消息，例如存到 MongoDB 中，即便 MongoDB 也支撑不下来瞬时写入百万、千万记录的情况，所以可以考虑使用消息队列。比如说我们可以在Java后端系统上面，用异步多线程的方法，向消息队列MQ中发送消息，这样Web系统发布公告消息的时候就不占用数据库正常的 CRUD 操作。系统消息保存在消息队列中，我们只是用它来做削峰填谷，系统消息最终还是要存储在数据库上面。于是我们可以这样设计，在用户登陆系统的时候，用异步线程从消息队列MQ中，接收该用户的系统消息，然后把系统消息存储在数据库中，最后消息队列MQ中的该条消息自动删除。因为用户的错峰登录，所以往数据库中写入消息的任务也变成了错峰写入。

## 1.3 什么是 RabbitMQ

RabbitMQ 是一个使用 Erlang 语言编写，且遵循 AMQP协议的开源消息队列系统，支持多种客户端（语言），用于在分布式系统中存储消息，转发消息，具有高可用，高可扩性，易用性等特征。

更详细的介绍可以直接看一下官网：

- [www.rabbitmq.com/](https://link.juejin.cn/?target=https%3A%2F%2Fwww.rabbitmq.com%2F "https://www.rabbitmq.com/")

总之这就是一种常见的消息队列，它的这些特点，都会在后面逐条讲解到，我们首先从入门下载安装部分先说起，然后再到使用。

# 2\. 下载与安装

一般来说，安装的方式有手动安装和 Docker 安装，大部分场景下，都会使用 Docker 安装，但是作为学习阶段，如果不是特别着急，学习一下手动安装，也不是什么坏事。

注：云服务器和虚拟机都可以，演示的 Linux 版本为 CentOS 7.9

## 2.1 手动安装

### 2.1.1 下载安装过程

注：可以在 Linux 中通过 yum 直接下载安装，这里选择了在自己的 Windows 主机先下载文件，然后再通过 FTP 传到 Linux 上，直接安装。可以避免虚拟机上因为网络而造成的一些下载问题。

1.  首先打开官网的下载目录，然后根据自己 Linux 的版本，选择版本。

    - 地址：[www.rabbitmq.com/download.ht…](https://link.juejin.cn/?target=https%3A%2F%2Fwww.rabbitmq.com%2Fdownload.html "https://www.rabbitmq.com/download.html")

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/62689ed4ca264c508bfacffe449ddcec~tplv-k3u1fbpfcp-zoom-1.image)

2.  因为 RabbitMQ 是 Erlang 语言编写的，所以还需要提供 Erlang 环境，接着去下载 Erlang。

    - 地址：[www.erlang-solutions.com/downloads](https://link.juejin.cn/?target=https%3A%2F%2Fwww.erlang-solutions.com%2Fdownloads "https://www.erlang-solutions.com/downloads")
          - A：此网站访问速度极慢，请耐心等待，或者需要挂上梯子
          - B：Erlang 版本需要与 RabbitMQ 匹配（如图，有最大和最小版本的限制）
                - 版本查看地址：[www.rabbitmq.com/which-erlan…](https://link.juejin.cn/?target=https%3A%2F%2Fwww.rabbitmq.com%2Fwhich-erlang.html "https://www.rabbitmq.com/which-erlang.html")
                - 这里选择了 RabbitMQ 3.8.14 和 Erlang 23.2.3

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/21192b8b56d54085afe785e16b500d3c~tplv-k3u1fbpfcp-zoom-1.image)

3\. 将文件上传到 Linux 中（我这里指定位置是 /usr/local/bin/rabbitmq ，可以自己更改选择） - 现在很多 Shell 软件都自带内置的 FTP 上传，例如 FinalShell，MobaXterm 等等 - 上传后的文件和目录位置如下

```shell
[root@centos7 rabbitmq]# ls
esl-erlang_23.2.3-1_centos_7_amd64.rpm  rabbitmq-server-3.8.14-1.el7.noarch.rpm
[root@centos7 rabbitmq]# pwd
/usr/local/bin/rabbitmq
复制代码
```

 4.     安装 Erlang 、Socat 和 RabbitMQ
    - Erlang 、Socat 都是 RabbitMQ 所依赖的

```shell
# 安装 Erlang，安装后执行 erl -v 显示版本号则代表成功
rpm -ivh esl-erlang_23.2.3-1_centos_7_amd64.rpm

# 安装 Socat 这里没有下载源文件，而是直接通过 yum 在线安装，因为它并不大
yum install -y socat

# 安装 RabbitMQ
rpm -ivh rabbitmq-server-3.8.14-1.el7.noarch.rpm
复制代码
```

 5.     安装结束，启动服务查看 RabbitMQ 是否可以启动成功

```shell
# 启动服务
systemctl start rabbitmq-server
# 开机自启
systemctl enable rabbitmq-server
# 停止服务
systemctl stop rabbitmq-server
# 查看服务状态
systemctl status rabbitmq-server.service
复制代码
```

如图所示，即安装启动成功

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa1a6627ffbc466db7eb2bc302a65eda~tplv-k3u1fbpfcp-zoom-1.image)

若安装错误，处理参考：

- [Linux之RabbitMQ安装各种问题处理](https://link.juejin.cn/?target=https%3A%2F%2Fblog.csdn.net%2Fhuijiangu4621%2Farticle%2Fdetails%2F83619707 "https://blog.csdn.net/huijiangu4621/article/details/83619707")
- [rabbitmq ERROR: epmd error for host deb：address \(cannot connect to host/port\)解决方法](https://link.juejin.cn/?target=https%3A%2F%2Fblog.csdn.net%2FQevery678%2Farticle%2Fdetails%2F102599863 "https://blog.csdn.net/Qevery678/article/details/102599863")

### 2.1.2 配置 Web 界面管理

上面的安装其实已经结束了，但是 RabbitMQ 提供给了我们一个 Web 形式的管理界面，默认是没有的，需要进行安装。

 1.     安装 Web 管理插件，然后重启服务

```shell
# 安装命令
rabbitmq-plugins enable rabbitmq_management

# 重启服务
systemctl restart rabbitmq-server
复制代码
```

 2.     一定要开放 Linux 防火墙 的 15672 端口，否则就会无法访问，在学习阶段，你甚至可以去查询命令把防火墙关掉
    - 对应服务器（阿里云，腾讯云等）就是在安全组中开放 15672 端口
    - 访问 Linux IP:15672 ，例如 `http://192.168.122.1:15672`

```shell
# 查询 15672 是否开放，一般默认都是 no
firewall-cmd --query-port=15672/tcp
# 开放指定端口 15672 
firewall-cmd --add-port=15672/tcp --permanent
# 重新载入
firewall-cmd --reload
# 再次查询，结果就是 yes 了
firewall-cmd --query-port=15672/tcp
复制代码
```

  3.  添加远程登录的账户

    - RabbitMQ 有一个默认账号和密码都是 guest 但是只能在 localhost 下访问

```shell
# 新增用户 用户名和密码都是 admin
rabbitmqctl add_user admin admin
复制代码
```

 4.     为远程登录的账户添加权限
    - administrator（超级管理员）：登录控制台、查看所有信息、操作用户、操作策略
    - monitoring（监控者）： 登录控制台、查看所有信息
    - policymaker（策略制定者）： 登录控制台、指定策略
    - managment（普通管理员）： 登录控制台

```shell
# 设置用户分配操作权限，admin 用户的权限为 administrator
rabbitmqctl set_user_tags admin administrator
复制代码
```

 5.     为用户添加资源权限
    - 因为 admin 已经是超级管理员权限了，所以其实不分配资源权限也可以，会默认去做。

```shell
# 命令格式为: set_permissions [-p <vhostpath>] <user> <conf> <write> <read>
# 这里即为 admin 用户开启 配置文件和读写的权限
rabbitmqctl set_permissions -p / admin ".*"".*"".*"
复制代码
```

6.  访问 Linux IP:15672 ，例如 `http://192.168.122.1:15672` ，输入刚才设置好的用户名密码 admin
    - 如图：访问成功

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/47642f3dc21144a49c745685212ab32b~tplv-k3u1fbpfcp-zoom-1.image)

#### 2.1.2.1 命令小结

1.  添加用户：`rabbitmqctl add_user <username> <password>`
2.  修改密码：`rabbitmqctl change_password <username> <newpass>`
3.  删除用户：`rabbitmqctl delete_user <username>`
4.  用户列表：`rabbitmqctl list_users`
5.  设置用户角色：`rabbitmqctl set_user_tags <username> <tag1,tag2>`
6.  删除用户所有角色：`rabbitmqctl set_user_tags <username>`
7.  为用户添加资源权限：`set_permissions [-p <vhostpath>] <user> <conf> <write> <read>`

使用：输入 rabbitmqctl ，则会提示可能使用的命令，然后 使用 rabbitmqctl hepl \<命令> 可以查看具体命令的使用方法和参数。

### 2.1.3 简单介绍 Web 界面管理

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fd2e344f68de4490bd3180bb8aec50b6~tplv-k3u1fbpfcp-zoom-1.image)

- Connections（连接）：此处用来管理与 RabbitMQ 建立连接后的生产者和消费者
- Channels（通道）：连接建立后，会形成通道，消息的投递获取依赖通道。
- Exchanges（交换机）：用来实现消息的路由
- Queues（队列）：存放消息的队列，消息等待被消费，消费后被移除队列。
- Admin（管理）：用于对管理用户，以及对应权限进行设置，如下图所示

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f20c85307aa404ea818279adfcabf30~tplv-k3u1fbpfcp-zoom-1.image)

Tags 就是用来指定用户的角色

- administrator（超级管理员）：登录控制台、查看所有信息、操作用户、操作策略
- monitoring（监控者）： 登录控制台、查看所有信息
- policymaker（策略制定者）： 登录控制台、指定策略
- managment（普通管理员）： 登录控制台

## 2.2 Docker 安装

在 Docker 中安装 RabbitMQ 不需要自己去考虑版本，环境等的各种冲突不兼容问题，是非常便捷的，我演示的这台虚拟机是一个 CentOS 7.9 裸机，所以我们从更新 yum，到安装 Docker 和 安装 RabbitMQ 按步骤都讲一下

### 2.2.1 配置 yum

 1.     更新 yum 到最新版

```shell
# 更新 yum
yum update

# 检查yum依赖的几个包 yum-utils 提供 yum-config-manager 功能， 后面两个是 devicemapper 用到的
yum install -y yum-utils device-mapper-persistent-data lvm2
复制代码
```

 2.     设置 yum 源为阿里云

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
复制代码
```

### 2.2.2 安装 docker

#### 2.2.2.1 步骤

 1.     使用 yum 安装 docker
    - docker-ce 是社区版的意思，ee为企业版

```shell
yum install docker-ce -y
复制代码
```

 2.     通过查看版本，检查安装是否成功

```shell
docker -v
复制代码
```

 3.     Docker 镜像加速（这里 \<你的ID >要换成自己的哈）

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://<你的ID>.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
复制代码
```

> - 国内从 DockerHub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。Docker 官方和国内很多云服务商都提供了国内加速器服务，例如：
> 
>   - 科大镜像：**[docker.mirrors.ustc.edu.cn/](https://link.juejin.cn/?target=https%3A%2F%2Fdocker.mirrors.ustc.edu.cn%2F "https://docker.mirrors.ustc.edu.cn/")**
>   - 网易：**[hub-mirror.c.163.com/](https://link.juejin.cn/?target=https%3A%2F%2Fhub-mirror.c.163.com%2F "https://hub-mirror.c.163.com/")**
>   - 阿里云：**https://\<你的ID>.mirror.aliyuncs.com**
>   - 七牛云加速器：**[reg-mirror.qiniu.com](https://link.juejin.cn/?target=https%3A%2F%2Freg-mirror.qiniu.com "https://reg-mirror.qiniu.com")**
> 
>   当配置某一个加速器地址之后，若发现拉取不到镜像，请切换到另一个加速器地址。国内各大云服务商均提供了 Docker 镜像加速服务，建议根据运行 Docker 的云平台选择对应的镜像加速服务。
> 
>   阿里云镜像获取地址：[cr.console.aliyun.com/cn-hangzhou…](https://link.juejin.cn/?target=https%3A%2F%2Fcr.console.aliyun.com%2Fcn-hangzhou%2Finstances%2Fmirrors%25EF%25BC%258C%25E7%2599%25BB%25E9%2599%2586%25E5%2590%258E%25EF%25BC%258C%25E5%25B7%25A6%25E4%25BE%25A7%25E8%258F%259C%25E5%258D%2595%25E9%2580%2589%25E4%25B8%25AD%25E9%2595%259C%25E5%2583%258F%25E5%258A%25A0%25E9%2580%259F%25E5%2599%25A8%25E5%25B0%25B1%25E5%258F%25AF%25E4%25BB%25A5%25E7%259C%258B%25E5%2588%25B0%25E4%25BD%25A0%25E7%259A%2584%25E4%25B8%2593%25E5%25B1%259E%25E5%259C%25B0%25E5%259D%2580%25E4%25BA%2586 "https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors%EF%BC%8C%E7%99%BB%E9%99%86%E5%90%8E%EF%BC%8C%E5%B7%A6%E4%BE%A7%E8%8F%9C%E5%8D%95%E9%80%89%E4%B8%AD%E9%95%9C%E5%83%8F%E5%8A%A0%E9%80%9F%E5%99%A8%E5%B0%B1%E5%8F%AF%E4%BB%A5%E7%9C%8B%E5%88%B0%E4%BD%A0%E7%9A%84%E4%B8%93%E5%B1%9E%E5%9C%B0%E5%9D%80%E4%BA%86")

#### 2.2.2.2 Docker 常见命令

**2.2.2.2.1 管理命令**

 -    就启动，停止，重启这些简单的命令使用 service 也是可以的，systemctl 功能稍微强大一些

```shell
# 启动 docker
systemctl docker start
# 停止 docker
systemctl docker stop
# 重启 docker
systemctl docker restart
# 查看 docker 状态
systemctl status docker
# 开机自启
systemctl enable docker
systemctl unenable docker
复制代码
```

**2.2.2.2.2 镜像命令**

```shell
# 导入镜像文件
docker load < xxx.tar.gz
# 查看安装的镜像
docker images
# 删除镜像
docker rmi 镜像名
复制代码
```

### 2.2.3 安装 RabbitMQ （任选其一）

注：直接用 2.2.3.2 一句话安装 会更好一些

#### 2.2.3.1 一步一步安装

 1.     获取 RabbitMQ 的镜像

```shell
docker pull rabbitmq:management
复制代码
```

 2.     创建并运行容器（具体参数在 3 中介绍）

```shell
docker run -id --name 容器名 -p 15672:15672 -p 5672:5672 rabbitmq:management
复制代码
```

#### 2.2.3.2 一句话安装

上面的安装方式，就是先获取到 RabbitMQ 镜像后再开始安装，这里是没有问题的，创建时会有一个问题，因为我们要安装 management 也就是它的 web 管理，如果不做一些处理，默认装好的是没有用户的，所以还需要像前面一样自己进去配置，而 Docker Hub 已经给出了我们配置的示例，即使用 `-e` 代表配置，使用 `RABBITMQ_DEFAULT_USER` 和 `RABBITMQ_DEFAULT_PASS` 配置用户名和密码

更多请查看 Docker Hub 官方给予例子中的 Setting default user and password 章节

[registry.hub.docker.com/\_/rabbitmq/](https://link.juejin.cn/?target=https%3A%2F%2Fregistry.hub.docker.com%2F_%2Frabbitmq%2F "https://registry.hub.docker.com/_/rabbitmq/")

 1.     执行安装

```shell
docker run -di --name myrabbitmq -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 -p 25672:25672 -p 61613:61613 -p 1883:1883 rabbitmq:management
复制代码
```

 2.     通过容器状态，查看是否运行成功

```shell
# 查看容器运行状态
docker ps -a
# 启动
docker start 容器名
# 停止
docker stop 容器名
# 退出命令行，不停止
exit
# 进入到node容器（如果开启了 -t 的情况）
docker exec -it 容器名 bash
复制代码
```

**2.2.3.2.1 参数介绍**

下面分别讲解一下这些参数的说明：

- `-i`：表示运行容器。

- `-t`：表示为容器保留交互的方式（命令行），即分配一个伪终端。所以常常会见到 `-it` 这样的搭配。

- `--name` ：为容器起个名字。

- `-v`：表示目录映射关系（前者是宿主机目录，后者是映射到宿主机上的目录），可以使用多个 `-v` 做多个目录或文件映射。注意：推荐做目录映射，在宿主机上做修改，然后共享到容器上。

- `-d`：表示创建一个守护式容器在后台运行（这样创建容器后不会自动登录容器，如果只加 `-i` `-t` 两个参数，创建后就会自动进去容器），即后端挂起运行。

- `-p`：表示端口映射，前者是宿主机端口，后者是容器内的映射端口。可以使用多个 `-p` 做多个端口映射，只有做了端口映射，才能被外界访问。

给大家举个例子：

```shell
# 创建容器，把容器 3000 端口映射到宿主机 3000 端口，把/demo映射到宿主机的/demo  face是我下载好的一个现成的镜像
docker run -d -it -p 3000:3000 -v /demo:/demo --name node face

# 例如，名为 node 的镜像中有一个需要执行的 python 程序，就可以通过如下命令进入刚才分配到的命令行中去执行这个程序
docker exec -it node bash
复制代码
```

- 因为使用了 `-t` 这个参数，所以可以分配到一个伪终端，通过 `docker exec \-it 容器名 bash` 进入命令行

- `-v` 目录映射后，进入容器后，也会有一个一模一样的 demo 文件夹，例如在其中可以执行 python 程序

**2.2.3.2.1 端口介绍**

4369 ：erlang发现端口

5672：client端通信端口

15672：管理界面ui端口

25672：server间内部通信端口

61613：不带TLS和带TLS的STOMP客户端

1883：不启用和启用TLS的MQTT客户端

比较关键的就是 5672 和 15672

更多端口详情可以访问官网文档

- [www.rabbitmq.com/networking.…](https://link.juejin.cn/?target=https%3A%2F%2Fwww.rabbitmq.com%2Fnetworking.html "https://www.rabbitmq.com/networking.html")

注：如果要通过远程连接，例如访问 web 管理页面的 15672 端口，Java 客户端连接的 5672 端口， 一定要进行一个开放操作，否则都连接不到。

 -    以下为基于 CentOS 7.9 开放 15672 端口的例子

```shell
# 查询 15672 是否开放，一般默认都是 no
firewall-cmd --query-port=15672/tcp
# 开放指定端口 15672 
firewall-cmd --add-port=15672/tcp --permanent
# 重新载入
firewall-cmd --reload
# 再次查询，结果就是 yes 了
firewall-cmd --query-port=15672/tcp
复制代码
```

 -    以下是关闭防火墙的命令

```shell
systemctl disable firewalld
systemctl stop firewalld   
复制代码
```

# 3\. RabbitMQ 协议和模型

安装结束后，就要进入主题，即用 Java 或者 Springboot 代码来实现 RabbitMQ的几种方式，但是想要很好的理解这几种路由交换方式，就需要对它的协议和架构模型有所了解。

## 3.1 协议

### 3.1.1 什么是协议？

协议，网络协议的简称，网络协议是通信计算机双方必须共同遵从的一组约定。如怎么样建立连接、怎么样互相识别等。只有遵守这个约定，计算机之间才能相互通信交流。它的三要素是：语法、语义、时序。

为了使数据在网络上从源到达目的，网络通信的参与方必须遵循相同的规则，这套规则称为协议（protocol），它最终体现为在网络上传输的数据包的格式。

#### 3.1.1.1 网络协议的三要素

1.  语法：数据与控制信息的结构和格式，以及数据出现的顺序。
2.  语义：解释控制信息每个部分的意义，以及规定了需要发出何种控制信息以及完成的动作做出何种响应。
3.  时序：对事件发生顺序的详细说明。

人们形象地把这三个要素描述为：做什么，怎么做，做的顺序。

举个例子 HTTP 协议

> 语法：HTTP 规定了请求报文和响应报文的格式 语义：客户端主动发起请求称为请求，服务端随之返回数据，称为响应 时序： 一个请求对应一个响应，而且先有请求后有响应

**3.1.1.1.1 面试题：为什么消息中间件不直接使用 HTTP 协议**

对于一个消息中间件来说，其主要责任就是负责数据传递，存储，分发，高性能和简洁才是我们所追求的，而 HTTP 请求报文头和响应报文头是比较复杂的，包含了Cookie，数据的加密解密，窗台吗，响应码等附加的功能，我们并不需要这么复杂的功能。

同时大部分情况下 HTTP 大部分都是短链接，在实际的交互过程中，一个请求到响应都很有可能会中断，中断以后就不会执行持久化，就会造成请求的丢失。这样就不利于消息中间件的业务场景，因为消息中间件可能是一个长期的获取信息的过程，出现问题和故障要对数据或消息执行持久化等，目的是为了保证消息和数据的高可靠和稳健的运行

### 3.1.2 RabbitMQ 的 AMQP 协议

RabbitMQ 的使用的协议是 AMQP（advanced message queuing protocol），它在2003年时被提出，最早用于解决金融领不同平台之间的消息传递交互问题。

AMQP 更准确的说是一种 binary wire-level protocol（链接协议）。这是其和 JMS 的本质差别，AMQP 不从 API 层进行限定，而是直接定义网络交换的数据格式。这使得实现了AMQP的 Provider（Producer） 天然性就是跨平台的。

相比较其它消息协议，其特性为：

1.  分布式事务支持
2.  消息的持久化支持
3.  高性能和高可靠的消息处理优势

### 3.1.3 架构模型

想要学习后面的几种消息具体的发送模式，这个模型图就必须理解清楚，因为这几种方式就是对这个模型不同程度的选择和缩减

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/de95cc101635428bb028a0e88e4b27fd~tplv-k3u1fbpfcp-zoom-1.image)

\- \`Producer\`：消息的生产者（发送消息的程序）。

- `Connection`：应用程序与Broker之间的网络连接。

- `Channel`：信道，即信息传输的通道，可以建立多个 Channel，每个 Channel 代表一个会话任务。

  - 信道是建立在 TCP 连接内的虚拟连接，信息的读写都通过信道传输，因为对于操纵系统而言，建立和销毁 TCP 是非常昂贵的，所以引入了信道的概念，以复用一条 TCP 连接。

- `Broker(Server)` ：标识消息队列服务器实体，例如这里就是 RabbitMQ Server。

- `Virtual Host`：虚拟主机，一个 Broker 中可以设置多个 Virtual Host，用作不同用户的权限隔离。

  - Broker 可以理解为整个数据库服务，而 Virtual Host 就是其中每个数据库的感觉，不同项目可以对应不同的数据库，其中有着项目所属的业务表等等。
  - 每个 Virtual Host 中，可以有若干个 Exchange 和 Queue。

- `Exchange`：交换机，用来接收生产者发送的消息，然后将这些消息根据路由键发送到队列。

- `Binding`：Exchange 和 Queue 之间的虚拟连接，Binding 中可以包括多个 Routing key。

- `Routing key`：路由规则，虚拟机用它来确认如何路由一个特定消息。

- `Queue`：消息队列，它是消息的容器，用来保存消息，每一条消息都能传入一个或者多个队列中，等待消费者消费，即取出这个消息。

- `Consumer`：消息的消费者（接收消息的程序）。

# 4\. Java 实现 RabbitMQ

## 4.1 环境搭建

官网介绍几种模型：[www.rabbitmq.com/getstarted.…](https://link.juejin.cn/?target=https%3A%2F%2Fwww.rabbitmq.com%2Fgetstarted.html "https://www.rabbitmq.com/getstarted.html")

截止目前为止，官网一共提供了 7 中模型的介绍，我们主要介绍前五种基本的模式，也有人将 Direct 和 Topic模式都归入 Routing 模式，也可以看做四大种。

### 4.1.1 创建 Java 项目

首先创建好一个不使用骨架的 Maven 项目，然后引入 RabbitMQ 依赖，还有单元测试依赖即可

```xml
<dependency>
   <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.10.0</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
</dependency>
复制代码
```

### 4.1.2 创建虚拟主机（可选）

在这里，我们创建了一个新的 Virtual Hosts，用来为这个Java项目服务，大家还可以创建一个新的用户，然后对其开启这个 Virtual Hosts 的访问权限（即将虚拟主机与用户绑定）。我们这里还是用 admin（我之前创建的一个管理员权限用户） 来演示。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/66a284c9c2304072a43c4dd5c0af1a9e~tplv-k3u1fbpfcp-zoom-1.image)

注：这部分不去做也可以，直接用 / 和 admin 用户也行

### 4.1.3 创建连接工具类

由于我们后面要演示多种例子，而每一次获取连接和释放连接、关闭资源等操作代码都是一致的，为了防止代码冗余，优化代码，更易理解，提取出一个工具类，这样大家将重心放在不同实现方式的对比上就行了。

 -    RabbitMqUtil 工具类

```java
public class RabbitMqUtil {
    /**
     * 主机名 即 Linux IP地址
     */
    private static String host = "";
    /**
     *  端口号 客户端访问默认都是 5672
     */
    private static int port = 0;
    /**
     * 虚拟主机 可以设置为默认的 / 或者自己创建出指定的虚拟主机
     */
    private static String virtualHost = "";
    /**
     * 用户名
     */
    private static String username = "";
    /**
     * 密码
     */
    private static String password = "";

    // 使用静态代码块为Properties对象赋值
    static {
        try {
            //实例化对象
            Properties properties = new Properties();
            //获取properties文件的流对象
            InputStream in = RabbitMqUtil.class.getClassLoader().getResourceAsStream("rabbitmq.properties");
            properties.load(in);
            // 分别获取 value
            host = properties.getProperty("host");
            port = Integer.parseInt(properties.getProperty("port"));
            virtualHost = properties.getProperty("virtualHost");
            username = properties.getProperty("username");
            password = properties.getProperty("password");

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取连接
     *
     * @return 连接
     */
    public static Connection getConnection() {
        try {
            // 创建连接工厂
            ConnectionFactory connectionFactory = new ConnectionFactory();
            // 设置连接 rabbitmq 主机
            connectionFactory.setHost(host);
            // 设置端口号
            connectionFactory.setPort(port);
            // 设置连接的虚拟主机（数据库的感觉）
            connectionFactory.setVirtualHost(virtualHost);
            // 设置访问虚拟主机的用户名和密码
            connectionFactory.setUsername(username);
            connectionFactory.setPassword(password);
            // 返回一个新连接
            return connectionFactory.newConnection();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 关闭通道和释放连接
     *
     * @param channel    channel
     * @param connection connection
     */
    public static void close(Channel channel, Connection connection) {
        try {
            if (channel != null) {
                channel.close();
            }
            if (connection != null) {
                connection.close();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
复制代码
```

 -    properties

```properties
host=192.168.122.1
port=5672
virtualHost=/rabbitmq_maven_01
username=admin
password=admin
复制代码
```

## 4.2 五种实现方式

说明：

- 队列名，消息等等字符串内容，更推荐定义成变量传入，我文中都是直接写在参数中的，这种魔法值的写法，并不是很优美。
- 生产者中使用了 Junit 单元测试，但是消费者中却在 main 函数中编写，这是因为，我们希望消费者处于一个持续运行等待的状态，如果使用 Junit 会导致，程序在执行一次后结束掉。
  - 除了在 main 函数中编写，还可以考虑使用 sleep 等待或者 while\(true\) 让程序不要直接终止掉。

### 4.2.1 简单队列模式（Hello Word）

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc8532fc790f4dc1b01018ef3b1eed59~tplv-k3u1fbpfcp-zoom-1.image)

\- \`Producer\`：消息的生产者（发送消息的程序）。

- `Queue`：消息队列，理解为一个容器，生产者向它发送消息，它把消息存储，等待消费者消费。
- `Consumer`：消息的消费者（接收消息的程序）。

#### 4.2.1.1 如何理解

由图所示，简单队列模式，一个生产者，经过一个队列，对应一个消费者。可以看做是点对点的一种传输方式，相较与 3.1.3 中的模型图，最主要的特点就是看不到 Exchange（交换机） 和 routekey（路由键） ，正是因为这种模式简单，所以并不会涉及到复杂的条件分发等等，因此也不需要用户去显式的考虑交换机和路由键的问题。

- 但是要注意，这种模式并不是生产者直接对接队列，而是用了默认的交换机，默认的交换机会把消息发送到和 routekey 名称相同的队列中去，这也是我们在后面代码中在 routekey 位置填写了队列名称的原因

#### 4.2.1.2 代码实现

**4.2.1.2.1 生产者代码**

```java
public class Producer {
    @Test
    public void sendMessage() throws IOException, TimeoutException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        Channel channel = connection.createChannel();
        // 通道绑定消息队列
        channel.queueDeclare("queue1",false,false,false,null);
        // 发布消息
        channel.basicPublish("","queue1",null,"This is rabbitmq message 001 !".getBytes());
        // 通过工具关闭channel和释放连接
        RabbitMqUtil.close(channel,connection);
    }
}
复制代码
```

1.  **通过工具类获取连接**
2.  **获取连接通道**：根据 3.1.3 的模型图可知，生产者需要在获取到连接后，再获取信道，才能去访问后面的交换机队列等。
3.  **通道绑定消息队列**：绑定队列前，应该绑定交换机，但是此模式中隐蔽了交换机的概念，背后使用了默认的交换机，所以直接绑定队列。
    - queueDeclare 方法解释
          - 参数1：queue（队列名称），如果队列不存在，则自动创建。
          - 参数2：durable（队列是否持久化），持久化可以保证服务器重启后此队列仍然存在。
          - 参数3：exclusive（排他队列）即是否独占队列，如果此项为 true，该队列仅对首次申明它的连接可见，并在连接断开时自动删除。
          - 参数4：autoDelete（自动删除），最后一个消费者将消息消费完毕后，自动删除队列。
          - 参数5：arguments（携带附加属性）。
4.  **发布消息**：此处可以指定消息队列的发送方法，以及内容等，因为此模式比较简单，所以没有涉及到全部参数，后面的模式会有详细的讲解
    - basicPublish 方法解释
          - 参数1：exchange（交换机名称）。
          - 参数2：routingKey（路由key），此处填写队列名，可理解为把消息发送到和 routekey 名称相同的队列中去。
          - 参数3：props（消息的控制状态），可以在此处控制消息的持久化。
                - 参数为：MessageProperties.PERSISTENT\_TEXT\_PLAIN
          - 参数4：body（消息主体），类型是一个字节数组，要转一下类型。
5.  **通过工具关闭channel和释放连接**：先关闭通道，再释放连接。

**4.2.1.2.2 消费者代码**

```java
public class Consumer {
    public static void main(String[] args) throws IOException, TimeoutException{
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        Channel channel = connection.createChannel();
        // 通道绑定消息队列
        channel.queueDeclare("queue1", false, false, false, null);
        // 消费消息
        channel.basicConsume("queue1", true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("new String(body): " + new String(body));
            }
        });
    }
}
复制代码
```

1.  **通过工具类获取连接**

2.  **获取连接通道**

3.  **通道绑定消息队列**

4.  **消费消息**：此处用来指定消费哪个队列的消息，以及一些机制和回调

    - basicConsume 方法解释
          - 参数1：queue（队列名称），即消费哪个队列的消息 。
          - 参数2：autoAck（自动应答）开始消息的自动确认机制，只要消费了就从队列删除消息。
          - 参数3：callback（消费时的回调接口），callback 的类型是 Consumer 这里使用了 DefaultConsumer 就是 Consumer 的一个实现类。其中重写 handleDelivery 方法，就可以获取到消费的数据内容了，这里主要使用了其中的 body，即查看消息主体，其他三个参数暂时还没用到，有兴趣可以先打印输出一下，能先有个大概的了解。

### 4.2.2 工作队列模式（Work Queue）

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3f597fb9aa854303976f06c23fc083a6~tplv-k3u1fbpfcp-zoom-1.image)

- `Producer`：消息的生产者（发送消息的程序）。

- `Queue`：消息队列，理解为一个容器，生产者向它发送消息，它把消息存储，等待消费者消费。

- `Consumer`：消息的消费者（接收消息的程序）。

  - 此处我们假设 Consumer1、Consumer2、Consumer3 分别为完成任务速度不一样快的消费者，这会引出此模式的一个重点问题。

#### 4.2.2.1 如何理解

工作模式由图可以看出，就是在简单队列模式的基础上，增加了多个消费者，也就是让多个消费者绑定同一个队列，共同去消费，这样能解决简单队列模式中，如果生产速速远大于消费速度，而导致的消息堆积现象。

- 因为消息被消费后就会消失，所以不必担心任务会重复执行。

#### 4.2.2.2 代码实现

注：工作队列模式有两种

1.  轮询模式：每个消费者均分消息
2.  公平分发模式（能者多劳）：按能力分发，处理速度快的分发的多，处理速度慢的分发的少

我们首先演示的是轮询模式，根据它的缺点，又能引出公平分发模式

下面只描述与上面有差异的部分，在简单模式中，这些基本的方法都有介绍过

**4.2.2.2.1 轮询模式-生产者代码**

```java
public class Producer {
    @Test
    public void sendMessage() throws IOException, TimeoutException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        Channel channel = connection.createChannel();
        // 通道绑定消息队列
        channel.queueDeclare("work", true, false, false, null);
        for (int i = 1; i <= 20; i++) {
            // 发布消息
            channel.basicPublish("", "work", null, (i + "号消息").getBytes());
        }
        // 通过工具关闭channel和释放连接
        RabbitMqUtil.close(channel, connection);
    }
}
复制代码
```

流程和简单队列模式基本一致，有一些小小的改动，生产者中主要就是加了层循环，因为有多个消费者，所以多发送一些消息，可以看出一些特点和问题。

**4.2.2.2.2 轮询模式-消费者代码**

 -    消费者 1

```java
public class Consumer1 {
    public static void main(String[] args) throws IOException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        final Channel channel = connection.createChannel();
        // 通道绑定消息队列
        channel.queueDeclare("work", true, false, false, null);
        // 消费消息
        channel.basicConsume("work", true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("消费者1号：消费-" + new String(body));
            }
        });
    }
}
复制代码
```

 -    消费者 2

```java
public class Consumer2 {
    public static void main(String[] args) throws IOException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        final Channel channel = connection.createChannel();
        // 通道绑定消息队列
        channel.queueDeclare("work", true, false, false, null);
        // 消费消息
        channel.basicConsume("work", true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者2号：消费-" + new String(body));
            }
        });
    }

复制代码
```

上述两个消费者都在 basicConsume中开启了自动 Ack 应答，这一点下面会详述，同时在消费者 1 中，增加了 sleep 2s 的语句，模拟消费者1处理消息速度慢，而消费者2处理消息速度快的场景。

运行结果：

 -    Consumer1

```java
消费者1号：消费-1号消息
消费者1号：消费-3号消息
消费者1号：消费-5号消息
消费者1号：消费-7号消息
消费者1号：消费-9号消息
消费者1号：消费-11号消息
消费者1号：消费-13号消息
消费者1号：消费-15号消息
消费者1号：消费-17号消息
消费者1号：消费-19号消息
复制代码
```

 -    Consumer2

```java
消费者2号：消费-2号消息
消费者2号：消费-4号消息
消费者2号：消费-6号消息
消费者2号：消费-8号消息
消费者2号：消费-10号消息
消费者2号：消费-12号消息
消费者2号：消费-14号消息
消费者2号：消费-16号消息
消费者2号：消费-18号消息
消费者2号：消费-20号消息
复制代码
```

观察执行过程：发现两个消费者虽然每个人最后都各自处理了一半的消息，而且是按照一人一条分配的，但是消费者2号处理速度快，一下子就全部处理完了，但是消费者1号，每一次处理都需要 2s 所以，只能缓慢的处理，而消费者2号就处于一个空闲浪费的情况了。

**如何切换为公平分发模式呢？**

这就和 basicConsume 中的第二个参数，开启自动确认消费有关了，它默认是 true，也就代表只要一旦拿到队列中分发给这个消费者的消息，我就会自动返回一个确认消费的标识，队列收到后就会自动删除掉队列中的消息。

- 但是这其中有一个很重要的问题，这种方式就是将风险交给了消费者，例如消费者收到了自己需要处理的 10 条消息，刚消费了 4 个，消费者宕机，挂掉了，后面的 6 个消息就丢失了。

如果想要修改为按能力分配的方式，有两个要点

1.  设置通道一次只能消费一个消息

2.  关闭消息的自动确认，手动确认消息

**4.2.2.2.3 公平分发模式-生产者代码**

```java
public class Producer {
    @Test
    public void sendMessage() throws IOException, TimeoutException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        Channel channel = connection.createChannel();
        // 一次只发送一条消息
        channel.basicQos(1);
        // 通道绑定消息队列
        channel.queueDeclare("work", true, false, false, null);
        for (int i = 1; i <= 20; i++) {
            // 发布消息
            channel.basicPublish("", "work", null, (i + "号消息").getBytes());
        }
        // 通过工具关闭channel和释放连接
        RabbitMqUtil.close(channel, connection);
    }
复制代码
```

**4.2.2.2.4 公平分发模式-消费者代码**

 -    消费者1

```java
public class Consumer1 {
    public static void main(String[] args) throws IOException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        final Channel channel = connection.createChannel();
        // 一次只接受一条未确认的消息
        channel.basicQos(1);
        // 通道绑定消息队列
        channel.queueDeclare("work", true, false, false, null);
        // 消费消息
        channel.basicConsume("work", false, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("消费者1号：消费-" + new String(body));
                // 返回 deliveryTag 代表队列可以删除此消息了
                channel.basicAck(envelope.getDeliveryTag(), false);
            }
        });
    }
}
复制代码
```

 -    消费者2

```java
public class Consumer2 {
    public static void main(String[] args) throws IOException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        final Channel channel = connection.createChannel();
        //步骤一:一次只接受一条未确认的消息
        channel.basicQos(1);
        // 通道绑定消息队列
        channel.queueDeclare("work", true, false, false, null);
        // 消费消息
        channel.basicConsume("work", false, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者2号：消费-" + new String(body));
                channel.basicAck(envelope.getDeliveryTag(), false);
            }
        });
    }

复制代码
```

运行结果：

 -    Consumer1

```java
消费者1号：消费-1号消息
复制代码
```

 -    Consumer2

```java
消费者2号：消费-2号消息
消费者2号：消费-3号消息
消费者2号：消费-4号消息
消费者2号：消费-5号消息
消费者2号：消费-6号消息
消费者2号：消费-7号消息
消费者2号：消费-8号消息
消费者2号：消费-9号消息
消费者2号：消费-10号消息
消费者2号：消费-11号消息
消费者2号：消费-12号消息
消费者2号：消费-13号消息
消费者2号：消费-14号消息
消费者2号：消费-15号消息
消费者2号：消费-16号消息
消费者2号：消费-17号消息
消费者2号：消费-18号消息
消费者2号：消费-19号消息
消费者2号：消费-20号消息
复制代码
```

### 4.2.3 发布与订阅模式（Fanout 广播）

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9fb92c0fe2374549859a8e24fddb592d~tplv-k3u1fbpfcp-zoom-1.image)

- `Producer`：消息的生产者（发送消息的程序）。
- `Exchange` ：交换机，负责发送消息给指定队列。
- `Queue`：消息队列，理解为一个容器，生产者向它发送消息，它把消息存储，等待消费者消费。
- `Consumer`：消息的消费者（接收消息的程序）。

#### 4.2.3.1 如何理解

Fanout 直译为 “扇出” 但是大家更多的会把它叫做广播或者发布与订阅，它是一种没有路由key的模式，生产者将消息发送给交换机，交换机会把所有消息复制同步到所有与它绑定过的队列上，而每个队列只能有一个消费者拿到这条消息，如果在一个消费者连接中，创建多个通道，则会出现争抢消息的结果。

#### 4.2.3.2 代码实现

注：下面只描述与上面有差异的部分，在简单模式中，这些基本的方法都有介绍过

**4.2.3.2.1 生产者代码**

```java
public class Producer {
    @Test
    public void sendMessage() throws IOException, TimeoutException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        final Channel channel = connection.createChannel();
        // 声明交换机
        channel.exchangeDeclare("order", "fanout");
        for (int i = 1; i <= 20; i++) {
            // 发布消息
            channel.basicPublish("order", "", null, "fanout!".getBytes());
        }
        // 通过工具关闭channel和释放连接
        RabbitMqUtil.close(channel, connection);
    }
}
复制代码
```

1.  **声明交换机**

    - exchangeDeclare 方法解释
          - 参数1：exchange（交换机名称），如果交换机不存在，则自动创建
          - 参数2：type（类型），此处选择 fanout 模式

2.  **发布消息**：在 basicPublish 方法的第一个参数中输入上述定义好的交换机的名字，第二个参数，路由键为空

    - 循环 20 条是为了演示消费者

**4.2.3.2.2 消费者代码**

 -    消费者1

```java
public class Consumer1 {
    public static void main(String[] args) throws IOException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        Channel channel = connection.createChannel();
        // 声明交换机
        channel.exchangeDeclare("order", "fanout");
        // 创建临时队列
        String queue = channel.queueDeclare().getQueue();
        // 绑定临时队列和交换机
        channel.queueBind(queue, "order", "");
        // 消费消息
        channel.basicConsume(queue, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者1号：消费-" + new String(body));
            }
        });
    }
}
复制代码
```

1.  **声明交换机**
2.  **创建临时队列**
3.  **绑定临时队列和交换机**
    - queueBind 方法解释
          - 参数1：queue（临时队列）
          - 参数2：exchange（交换机）
          - 参数3：routingKey（路由key）

 -    消费者2：演示了一个连接中，多个通道的情况

```java
public class Consumer2 {
    public static void main(String[] args) throws IOException {
       // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        
        // 获取连接通道
        Channel channel = connection.createChannel();
        Channel channel2 = connection.createChannel();
        
        // 声明交换机
        channel.exchangeDeclare("order", "fanout");
        channel2.exchangeDeclare("order", "fanout");
        
        // 创建临时队列
        String queue = channel.queueDeclare().getQueue();
        System.out.println(queue);
        
        // 绑定临时队列和交换机
        channel.queueBind(queue, "order", "");
        channel2.queueBind(queue, "order", "");
        
        // 消费消息
        channel.basicConsume(queue, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者2号：消费-" + new String(body));
            }
        });
        
        // 消费消息
        channel2.basicConsume(queue, true, new DefaultConsumer(channel2) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者2-2号：消费-" + new String(body));
            }
        });
    }
}
复制代码
```

运行结果：

```java
消费者2号：消费-fanout!
消费者2号：消费-fanout!
消费者2-2号：消费-fanout!
消费者2号：消费-fanout!
消费者2号：消费-fanout!
消费者2号：消费-fanout!
消费者2号：消费-fanout!
消费者2号：消费-fanout!
消费者2号：消费-fanout!
消费者2号：消费-fanout!
消费者2号：消费-fanout!
消费者2-2号：消费-fanout!
消费者2-2号：消费-fanout!
消费者2-2号：消费-fanout!
消费者2-2号：消费-fanout!
消费者2-2号：消费-fanout!
消费者2-2号：消费-fanout!
消费者2-2号：消费-fanout!
消费者2-2号：消费-fanout!
消费者2-2号：消费-fanout!
复制代码
```

**4.2.3.2.3 为什么消费者中也声明交换机？**

从上面的代码中可以看出，在 Producer 和 Conusmer 中我们都分别声明了交换机，但是消费者由图可知，并不会与交换机有直接的接触，为什么消费者中也声明交换机呢？

这是为了保证 Producer 或者 Producer 执行的时候，永远不会因为交换机还没被声明而出错，例如你只在 Producer 声明了交换机，那么你就必须先启动 Producer ，如果直接执行 Conusmer 此时交换机就还不存在，就会报错。而全部写入声明，则可以保证不论先启动谁，都会声明到交换机。

### 4.2.4 路由模式（ Routing / Direct）

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3f5f4df5700a4d77a2f1fbc75bc02211~tplv-k3u1fbpfcp-zoom-1.image)

- `Producer`：消息的生产者（发送消息的程序）。
- `Exchange` ：交换机，负责发送消息给指定队列。
- `routingKey`：路由key，即上图的 key1，key2 等，相当于在交换机和队列之间又加了一层限制
- `Queue`：消息队列，理解为一个容器，生产者向它发送消息，它把消息存储，等待消费者消费。
- `Consumer`：消息的消费者（接收消息的程序）。

#### 4.2.4.1 如何理解

路由模式的交换机类型是 direct，与 fanout 模式相比，多了路由 key 这个概念。生产者发送携带指定 routingKey（路由key） 的消息到交换机，交换机拿着此 routingKey 去找到绑定了这个 routingKey 的队列，然后发送到此队列，一个队列可以绑定多个 routingKey 。

#### 4.2.4.2 代码实现

**4.2.4.2.1 生产者代码**

```java
public class Producer {
    @Test
    public void sendMessage() throws IOException, TimeoutException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        Channel channel = connection.createChannel();
        // 声明交换机
        channel.exchangeDeclare("order_direct", "direct");
        // 指定 routingKey 
        String key = "info";
        // 发布消息
        channel.basicPublish("order_direct", key, null, ("发送给指定路由" + key + "的消息").getBytes());
        // 通过工具关闭channel和释放连接
        RabbitMqUtil.close(channel, connection);
    }
}
复制代码
```

1.  指定 routingKey ，即在 basicPublish 方法 的第二个参数中，指定 key 的值

**4.2.4.2.2 消费者代码**

 -    消费者 1

```java
public class Consumer1 {
    public static void main(String[] args) throws IOException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        Channel channel = connection.createChannel();
        // 声明交换机
        channel.exchangeDeclare("order_direct", "direct");
        // 获取临时队列
        String queue = channel.queueDeclare().getQueue();
        // 绑定临时队列和交换机
        channel.queueBind(queue, "order_direct", "info");
        channel.queueBind(queue, "order_direct", "error");
        channel.queueBind(queue, "order_direct", "warn");
        // 消费消息
        channel.basicConsume(queue, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者1：消费-" + new String(body));
            }
        });
    }
}
复制代码
```

1.  只是在绑定队列和交换机的时候，增加了 key 这个值

 -    消费者2

```java
public class Consumer2 {
    public static void main(String[] args) throws IOException {
         // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        Channel channel = connection.createChannel();
        // 声明交换机
        channel.exchangeDeclare("order_direct", "direct");
        // 获取临时队列
        String queue = channel.queueDeclare().getQueue();
        // 绑定临时队列和交换机
        channel.queueBind(queue, "order_direct", "error");
        // 消费消息
        channel.basicConsume(queue, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者2：消费-" + new String(body));
            }
        });
    }
}
复制代码
```

运行结果：只有消费者 1 收到了消息

```java'
消费者1：消费-发送给指定路由info的消息
复制代码
```

### 4.2.5 通配符匹配模式（Topic）

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d85e748a29f2423b91427dba96b3e721~tplv-k3u1fbpfcp-zoom-1.image)

- `Producer`：消息的生产者（发送消息的程序）。
- `Exchange` ：交换机，负责发送消息给指定队列。
- `routingKey`：路由key，即上图的 key1，key2 等，相当于在交换机和队列之间又加了一层限制
  - 但是 Topic 中的 key 为通配符的形式，这样可以大大的提高效率
- `Queue`：消息队列，理解为一个容器，生产者向它发送消息，它把消息存储，等待消费者消费。
- `Consumer`：消息的消费者（接收消息的程序）。

#### 4.2.5.1 如何理解

通配符匹配模式的交换机类型为 topic，因为它与 Direct 模式很相似，所以大家有时候也会把 Direct 模式和 Topic 共同归入路由模式下，它们的区别就是，Direct 模式的 routingKey 是一个指定的值，而 Topic 模式的 routingKey 可以使用通配符， 而且一般都是由一个或多个单词组成，多个单词之间以”.”分割，例如： ideal.insert。

- `*` ：匹配正好一个词，例如： `order.*` 可以匹配到 order.insert
- `#`：匹配一个或者多个词，例如：`order.#` 可以匹配到 order.insert.common
  - `#` 就像一个多层的概念，而 `*` 只是一个单层的概念

#### 4.2.5.2 代码实现

**4.2.5.2.1 生产者代码**

```java
public class Producer {
    @Test
    public void sendMessage() throws IOException, TimeoutException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        Channel channel = connection.createChannel();
        channel.exchangeDeclare("order_topic", "topic");
        // 声明交换机
        String key = "user.query.all";
        // 发布消息
        channel.basicPublish("order_topic", key, null, ("发送给指定路由" + key + "的消息").getBytes());
        RabbitMqUtil.close(channel, connection);
    }
}
复制代码
```

**4.2.5.2.2 消费者代码**

 -    消费者1

```java
public class Consumer1 {
    public static void main(String[] args) throws IOException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        Channel channel = connection.createChannel();
        // 声明交换机
        channel.exchangeDeclare("order_topic", "topic");
        // 获取临时队列
        String queue = channel.queueDeclare().getQueue();
        // 指定路由key
        String key = "user.*";
        channel.queueBind(queue, "order_topic", key);
        // 发布消息
        channel.basicConsume(queue, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者1：消费-" + new String(body));
            }
        });
    }
}
复制代码
```

 -    消费者2

```java
public class Consumer2 {
    public static void main(String[] args) throws IOException {
        // 通过工具类获取连接
        Connection connection = RabbitMqUtil.getConnection();
        // 获取连接通道
        Channel channel = connection.createChannel();
        // 声明交换机
        channel.exchangeDeclare("order_topic", "topic");
        // 获取临时队列
        String queue = channel.queueDeclare().getQueue();
        // 指定路由key
        String key = "user.#";
        channel.queueBind(queue, "order_topic", key);
        channel.basicConsume(queue, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者2：消费-" + new String(body));
            }
        });
    }
}
复制代码
```

运行结果：只有消费者 2 收到了消息，因为消息是一个多层的结构，只有 `user.#` 能匹配到

```java'
消费者2：消费-发送给指定路由user.query.all的消息
复制代码
```

# 5\. Springboot 实现 RabbitMQ

SpringBoot 提供 Spring For RabbitMQ 的启动器，同时提供了一系列注解以及 RabbitTemplate 供我们使用，能够极大的简化开发 RabbitMQ 的步骤，下面分别演示了【5.1 基于纯注解】 以及【 5.2 基于注解 + 配置类】 的写法，其使用方式大同小异，只是声明和绑定队列交换机等的位置不同。一般认为后者更好维护管理，任选其一即可。

环境准备：

1.  首先创建 SprinBoot 项目，然后选择 RabbitMQ 的启动器，以及单元测试等基本启动器
2.  编写 yml 配置文件，编写连接 RabbitMQ 需要的数据

RabbitMQ 依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
复制代码
```

yml 配置文件

```yml
spring:
  rabbitmq:
    host: 192.168.122.1 # 服务器地址
    port: 5672 # tcp端口
    username: admin # 用户名
    password: admin # 用户密码
    virtual-host: /rabbitmq_springboot_01 # 虚拟主机
复制代码
```

## 5.1 基于纯注解

注：此方式没有创建配置类来管理队列以及交换机的声明和绑定等，而是全部通过注解的方式直接在消费者中写入

### 5.1.1 简单队列模式

所有生产消息的代码，我们都放到 Test 中去做

 -    生产者

```java
@SpringBootTest(classes = RabbitmqSpringbootApplication.class)
@RunWith(SpringRunner.class)
public class RabbitMqTest {
    /**
     * 注入 RabbitTemplate
     */
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void testSimpleSendMessage() {
        rabbitTemplate.convertAndSend("simple_queue", "This is a message !");
    }
}
复制代码
```

1.  第一步就是注入 SpringBoot 提供给我们的 RabbitTemplate
2.  通过 RabbitTemplate 的 convertAndSend 方法用来发送消息，他有多种重载方式，今天分别会用到 2 个 和 3 个参数的
    - convertAndSend 方法详解（两个参数）
          - 参数1：routingKey（路由key）
          - 参数2：object（发送的消息正文）
    - convertAndSend 方法详解（三个参数）
          - 参数1：exchange（交换机）
          - 参数2：routingKey（路由key）
          - 参数3：object（发送的消息正文）

 -    消费者

```java
// 注入容器
@Component
// 监听 RabbitMQ
@RabbitListener(queuesToDeclare = @Queue(value = "simple_queue", durable = "true", exclusive = "false", autoDelete = "false"))
public class SimpleConsumer {
    // 自动回调
    @RabbitHandler
    public void receiveMessage(String message) {
        System.out.println("消费者：" + message);
    }
}
复制代码
```

1.  **注入容器**

2.  **监听 RabbitMQ**，在 \@RabbitListener 注解中，可以实现，队列的声明，以及后面交换机与队列的绑定等

    - \@Queue 可以有四个参数，因为其各有默认值，所以只给定 value 值，就会按照 持久化，非独占，非自动删除的方式默认创建
          - 参数1：value（队列名）
          - 参数2：durable （ 持久化消息队列）RabbitMQ 重启后，队列仍存在，默认 true
          - 参数3：exclusive（是否独占） 表示该消息队列是否只在当前 Connection 生效，默认是 false
          - 参数4：auto-delete（自动删除）表示消息队列没有在使用时将被自动删除，默认是 false

3.  在方法上添加 \@RabbitHandler 注解，就能够实现自动回调，这样我们就能拿到生产者中的消息了

    - 注：receiveMessage 这个方法的参数类型，取决于你在生产者有发送了什么类型的数据

### 5.1.2 工作队列模式

#### 5.1.2.1 轮询模式

 -    生产者：没什么好说的，因为工作模式有多个消费者，所以多发送几条消息

```java
@SpringBootTest(classes = RabbitmqSpringbootApplication.class)
@RunWith(SpringRunner.class)
public class RabbitMqTest {
    /**
     * 注入 RabbitTemplate
     */
    @Autowired

    @Test
    public void testWorkSendMessage() {
        for (int i = 0; i < 20; i++) {
            rabbitTemplate.convertAndSend("work_queue", "This is a message !, 序号：" + i);
        }
    }
}
复制代码
```

 -    消费者

```java
@Component
public class WorkConsumer {  
    // 监听 RabbitMQ
    @RabbitListener(queuesToDeclare = @Queue("work_queue"))
    // 消费者1
    public void receiveMessage1(String message) {
        System.out.println("消费者1：" + message);
   
    // 监听 RabbitMQ
    @RabbitListener(queuesToDeclare = @Queue("work_queue")
    // 消费者2
    public void receiveMessage2(String message) {
        System.out.println("消费者2：" + message);
    }    
}
复制代码
```

1.  \@RabbitListener 注解，既可以放在类上，也可以放在方法上，例如上述代码，我们就分别放在了两个方法上，用来指代不同的消费者。
    - 但是如果在类上加入 \@RabbitListener 注解，而在下面两个方法中，添加 \@RabbitHandler 注解则会报错，需要分别为每个消费者都创建一个类

#### 5.1.2.2 公平模式（按能力分配）

**5.1.2.2.1 修改配置文件的方式**

- 生产者不变

  - 修改配置文件 yml / properties

```yml
spring:
  rabbitmq:
    host: 192.168.122.1 # 服务器地址
    port: 5672 # tcp端口
    username: admin # 用户名
    password: admin # 用户密码
    virtual-host: /rabbitmq_springboot_01 # 虚拟主机
	# 新增部分
    listener:
      simple:
        acknowledge-mode: manual # 开启 ack 手动应答
        prefetch: 1 # 每次只能消费 1 条消息
复制代码
```

1.  acknowledge-mode 选项介绍
    - auto：自动确认，为默认选项
    - manual：手动确认（按能力分配就需要设置为手动确认）
    - none：不确认，发送后自动丢弃

 -    消费者

```java
@Component
public class WorkConsumer {
	// 监听 RabbitMQ
    @RabbitListener(queuesToDeclare = @Queue("work_queue"))
    // 消费者 1
    public void receiveMessage(String body, Message message, Channel channel) throws IOException {
        try {
            // 打印输出消息主题
            System.out.println("消费者1：" + body);
            // 返回 deliveryTag 代表队列可以删除此消息了
            channel.basicAck(message.getMessageProperties().getDeliveryTag(),false);
        } catch (IOException e) {
            e.printStackTrace();
            // 消费者告诉队列信息消费失败
            channel.basicNack(message.getMessageProperties().getDeliveryTag(), false, true);
        }
    }
	
   // 监听 RabbitMQ
    @RabbitListener(queuesToDeclare = @Queue("work_queue"))
    // 消费者 2
    public void receiveMessage2(String body, Message message, Channel channel) throws IOException{
        try {
            // 延迟 2s 代表处理业务慢
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        try {
			// 打印输出消息主题
            System.out.println("消费者2：" + body);
            // 返回 deliveryTag 代表队列可以删除此消息了
            channel.basicAck(message.getMessageProperties().getDeliveryTag(), false);
        } catch (IOException e) {
            e.printStackTrace();
            // 消费者告诉队列信息消费失败
            channel.basicNack(message.getMessageProperties().getDeliveryTag(), false, true);
        }
    }
}
复制代码
```

1.  因为在 yml 配置中开启了手动确认，所以，需要在成功和失败后分别返回确认消息

2.  basicAck 方法解释

    - 参数1：deliveryTag（交付标志，即该消息的index），返回即代表确认收到消息，队列可以删除此消息了
    - 参数2：mutiple（是否批量）选择 true 将一次性拒绝所有小于 deliveryTag 的消息

3.  basicNack 方法解释

    - 参数 1 | 参数 2 同上
    - 参数3：requeue（被拒绝的是否重新进入队列）

运行结果：

```java
消费者1：This is a message !, 序号：2
消费者1：This is a message !, 序号：3
消费者1：This is a message !, 序号：4
消费者1：This is a message !, 序号：5
消费者1：This is a message !, 序号：6
消费者1：This is a message !, 序号：7
消费者1：This is a message !, 序号：8
消费者1：This is a message !, 序号：9
消费者1：This is a message !, 序号：10
消费者1：This is a message !, 序号：11
消费者1：This is a message !, 序号：12
消费者1：This is a message !, 序号：13
消费者1：This is a message !, 序号：14
消费者1：This is a message !, 序号：15
消费者1：This is a message !, 序号：16
消费者1：This is a message !, 序号：17
消费者1：This is a message !, 序号：18
消费者1：This is a message !, 序号：19
消费者1：This is a message !, 序号：20
    
消费者2：This is a message !, 序号：1
复制代码
```

到现在已经实现了修改配置文件的方式实现按能力分配，补充几个配置的内容，我们上面只用了一部分，其他的方便大家参考，yml 和 properties 大家自己选择即可

```properties
# 发送确认
spring.rabbitmq.publisher-confirm-type=correlated
# spring.rabbitmq.publisher-confirms=true(旧版)
# 发送回调
spring.rabbitmq.publisher-returns=true
# 消费手动确认
spring.rabbitmq.listener.direct.acknowledge-mode=manual
spring.rabbitmq.listener.simple.acknowledge-mode=manual
# 并发消费者初始化值
spring.rabbitmq.listener.simple.concurrency=1
# 并发消费者的最大值
spring.rabbitmq.listener.simple.max-concurrency=10
# 每个消费者每次监听时可拉取处理的消息数量
# 在单个请求中处理的消息个数，他应该大于等于事务数量(unack的最大数量)
spring.rabbitmq.listener.simple.prefetch=1
# 是否支持重试
spring.rabbitmq.listener.simple.retry.enabled=true
复制代码
```

**5.1.2.2.1 配置工厂的方式**

```java
/**
 * 设置消费者的确认机制，并达到能者多劳的效果
 *
 * @param connectionFactory 连接工厂
 * @return
 */
@Bean("workListenerFactory")
public RabbitListenerContainerFactory myFactory(ConnectionFactory connectionFactory) {
    SimpleRabbitListenerContainerFactory containerFactory =
        new SimpleRabbitListenerContainerFactory();
    containerFactory.setConnectionFactory(connectionFactory);
    // 修改为手动确认
    containerFactory.setAcknowledgeMode(AcknowledgeMode.MANUAL);
    // 拒绝策略,true 回到队列 false丢弃，默认是true
    containerFactory.setDefaultRequeueRejected(true);
    // 默认的PrefetchCount是250 修改为 1
    containerFactory.setPrefetchCount(1);

    return containerFactory;
}
复制代码
```

 -    消费者修改

```java
@RabbitListener(queuesToDeclare = @Queue("work_queue"))
// 将上面的监听，增加 containerFactory 属性，然后将配置好的工厂传入
@RabbitListener(queuesToDeclare = @Queue("work_queue"), containerFactory = "workListenerFactory")
复制代码
```

### 5.1.3 发布与订阅模式

 -    生产者

```java
@SpringBootTest(classes = RabbitmqSpringbootApplication.class)
@RunWith(SpringRunner.class)
public class RabbitMqTest {
    /**
     * 注入 RabbitTemplate
     */
    @Autowired

    @Test
    public void testFanoutSendMessage() {
        rabbitTemplate.convertAndSend("order_exchange", "", "This is a message !");
    }
}
复制代码
```

1.  因为从这个模式开始，就涉及到交换机了，所以用的是三个参数的方法

 -    消费者

```java
@Component
public class FanoutConsumer {
    // 绑定临时队列和交换机
    @RabbitListener(bindings = {
            @QueueBinding(
                value = @Queue(), // 临时队列
                exchange = @Exchange(name = "order_exchange", type = "fanout") // 交换机与类型
            )
    })
    public void receiveMessage1(String message) {
        System.out.println("消费者1：" + message);
    }

    // 绑定临时队列和交换机
    @RabbitListener(bindings = {
            @QueueBinding(
                value = @Queue(), // 临时队列
                exchange = @Exchange(name = "order_exchange", type = "fanout") // 交换机与类型
            )
    })
    public void receiveMessage2(String message) {
        System.out.println("消费者2：" + message);
    }
}
复制代码
```

### 5.1.4 路由模式（Direct）

 -    生产者

```java
@SpringBootTest(classes = RabbitmqSpringbootApplication.class)
@RunWith(SpringRunner.class)
public class RabbitMqTest {
    /**
     * 注入 RabbitTemplate
     */
    @Autowired

    @Test
    public void testDirectSendMessage() {
        rabbitTemplate.convertAndSend("direct_exchange", "info", "This is a message !");
    }
}
复制代码
```

 -    消费者

```java
@Component
public class DirectConsumer {
    // 绑定临时队列和交换机
    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue(), // 临时队列
                    exchange = @Exchange(name = "direct_exchange", type = "direct"), // 交换机和类型
                    key = {"info", "warn", "error"} // 路由key
            )

    })
    public void receiveMessage1(String message) {
        System.out.println("消费者1：" + message);
    }

     // 绑定临时队列和交换机
    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue(), // 临时队列
                    exchange = @Exchange(name = "direct_exchange", type = "direct"), // 交换机和类型
                    key = {"info", "warn", "error"} // 路由key
            )

    })
    public void receiveMessage2(String message) {
        System.out.println("消费者2：" + message);
    }
}
复制代码
```

### 5.1.5 主题模式

 -    生产者

```java
@SpringBootTest(classes = RabbitmqSpringbootApplication.class)
@RunWith(SpringRunner.class)
public class RabbitMqTest {
    /**
     * 注入 RabbitTemplate
     */
    @Autowired

    @Test
    public void testTopicSendMessage() {
        rabbitTemplate.convertAndSend("topic_exchange", "order.insert.common", "This is a message !");
    }
}
复制代码
```

 -    消费者

```java
@Component
public class TopicConsumer {
    // 绑定临时队列和交换机
    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue(), // 临时队列
                    exchange = @Exchange(name = "topic_exchange", type = "topic"), // 交换机和类型
                    key = {"order.*"} // 通配符路由key
            )

    })
    public void receiveMessage1(String message) {
        System.out.println("消费者1：" + message);
    }

    // 绑定临时队列和交换机
    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue(), // 临时队列
                    exchange = @Exchange(name = "topic_exchange", type = "topic"), // 交换机和类型
                    key = {"order.*"} // 通配符路由key
            )
    })
    public void receiveMessage2(String message) {
        System.out.println("消费者2：" + message);
    }
}
复制代码
```

## 5.2 基于注解 + 配置类

其实这种方式，就是将交换机，队列的声明和绑定都在配置类中进行，一个是消费者中的注解变的简洁了，再有就是统一管理，更加条理，而且生产者和消费者引用的时候也更加方便，日后修改的时候，也不需要对每一处都修改。

由于篇幅过长了，这里演示最复杂的 Topic 方式，其他的也是信手拈来。

 -    配置类

```java
@Configuration
public class RabbitMqConfiguration {
    
    public static final String TOPIC_EXCHANGE = "topic_order_exchange";
    public static final String TOPIC_QUEUE_NAME_1 = "test_topic_queue_1";
    public static final String TOPIC_QUEUE_NAME_2 = "test_topic_queue_2";
    public static final String TOPIC_ROUTINGKEY_1 = "test.*";
    public static final String TOPIC_ROUTINGKEY_2 = "test.#";

    @Bean
    public TopicExchange topicExchange() {
        return new TopicExchange(TOPIC_EXCHANGE);
    }

    @Bean
    public Queue topicQueue1() {
        return new Queue(TOPIC_QUEUE_NAME_1);
    }

    @Bean
    public Queue topicQueue2() {
        return new Queue(TOPIC_QUEUE_NAME_2);
    }

    @Bean
    public Binding bindingTopic1(){
        return BindingBuilder.bind(topicQueue1())
                .to(topicExchange())
                .with(TOPIC_ROUTINGKEY_1);
    }
    @Bean
    public Binding bindingTopic2(){
        return BindingBuilder.bind(topicQueue2())
                .to(topicExchange())
                .with(TOPIC_ROUTINGKEY_2);
    }

}
复制代码
```

1.  **添加 \@Configuration 注解**：表明这是一个配置类

2.  **定义常量**：将交换机名，队列名，路由key 等都可以创建为常量，调用，管理和修改都非常方便，还可以创建出一个专门的 RabbitMQ 的常量类。

3.  **定义交换机**：因为这个例子是 Topic 所以选择 TopicExchange 类型

4.  **定义队列**：传入队列名常量即可，因为持久化等存在默认值，也可以自己自定持久化，是否独占等参数

5.  **绑定交换机和队列**：利用 BindingBuilder 的 bind 方法绑定队列，to 绑定到指定交换机，with 传入路由key

 -    生产者

```java
@SpringBootTest(classes = RabbitmqSpringbootApplication.class)
@RunWith(SpringRunner.class)
public class RabbitMqTest {
    /**
     * 注入 RabbitTemplate
     */
    @Autowired

    @Test
    public void testTopicSendMessage() {
        rabbitTemplate.convertAndSend(RabbitMqConfiguration.TOPIC_EXCHANGE, "test.order.insert", "This is a message !");
    }
}
复制代码
```

 -    消费者

```java
@Component
public class TopicConsumer {
	// 绑定队列即可
    @RabbitListener(queues = {RabbitMqConfiguration.TOPIC_QUEUE_NAME_1})
    public void receiveMessage1(String message) {
        System.out.println("消费者1：" + message);
    }
	
    // 绑定队列即可
    @RabbitListener(queues = {RabbitMqConfiguration.TOPIC_QUEUE_NAME_2})
    public void receiveMessage2(String message) {
        System.out.println("消费者2：" + message);
    }
}
复制代码
```