# RabbitMQ



### RabbitMQ是一个开源的消息代理和队列服务器 基于AMQP协议

好处:  开源 性能优秀 稳定性保障

提供可靠性消息投递模式(confirm)  返回模式 (return)

与SpringAMQP完美整合 API 丰富

集群模式丰富，表达式配置，HA模式，镜像队列模型

保证数据不丢失的前提做到高可靠。可用性

### 高性能的原因：

Erlang语言最初在于交换机领域的架构模式，这样使得RabbitMQ在Broker之间进行数据交互的性能是非常优秀的

Erang的优点：有着和原生Socket一样的延迟

### AMQP高级消息队列协议

是一个规范

是具有现代性特征的二进制协议。是一个提供统一消息服务的应用层标准高级消息队列协议，是应用层协议的一个开发标准，为面向消息的中间件设计。

AMQP核心概念

**Server**:又称Broker,接受客户端的连接，实现AMQP实体服务

**Connection**：连接，应用程序与Broker的网络连接

**Channel**：网络信道，几乎所有的操作都在Channel中进行，Channel是进行消息读写的通道。客户端可创建多个Channel,每个Channel代表一个会话任务。

**Message**:消息，服务器和应用程序之间传送的数据，由Properties和Body组成。Properties可以对消息进行修饰，比如消息的优先级、延迟等高级特性；Body则是消息体内容。

**VirtualHost**: 虚拟地址，用于进行逻辑隔离，最上层的消息路由。一个Virtual Host 里面可以有诺干个 Exchange和 Queue, 同一个里面不能有相同名称的Exchange 或 Queue

**Exchange**:交换机，接收消息，根据路由键转发消息到绑定的队列  一个交换机可以绑定多个 Queue

**Bingding**: Exchange和Queue之间的虚拟连接，包含 routingKey

**Routing key**: 一个路由规则，虚拟机可用它来确定如何路由一个特定的消息

**Queue**:也称Message Queue,消息队列，保存消息并将它们转发给消费者



P(生产) ->{ exchange -> queue} -> c(消费)



Exchange  routing key

# 安装使用

官网地址 : http://www.rabbitma.com/

提前准备: 安装Linux必要依赖包

下载RabbitMQ 安装包

修改配置

```
准备：
yum install 
build-essential openssl openssl-devel unixODBC unixODBC-devel 
make gcc gcc-c++ kernel-devel m4 ncurses-devel tk tc xz

下载：
wget www.rabbitmq.com/releases/erlang/erlang-18.3-1.el7.centos.x86_64.rpm
wget http://repo.iotti.biz/CentOS/7/x86_64/socat-1.7.3.2-5.el7.lux.x86_64.rpm
wget www.rabbitmq.com/releases/rabbitmq-server/v3.6.5/


配置文件：
vim /usr/lib/rabbitmq/lib/rabbitmq_server-3.6.5/ebin/rabbit.app
比如修改密码、配置等等，例如：loopback_users 中的 <<"guest">>,只保留guest
服务启动和停止：
启动 rabbitmq-server start &
停止 rabbitmqctl app_stop

管理插件：rabbitmq-plugins enable rabbitmq_management
访问地址：http://192.168.11.76:15672/


rpm 安装顺序
rpm -ivh erlang-18.3-1.el7.centos.x86_64.rpm
socat-1.7.3.2-5.el7.lux.x86_64.rpm
rabbitmq-server-3.6.5-1.noarch.rpm
异常:

连接不上 请开启端口
firewall-cmd --add-port=15672/tcp --permanent
firewall-cmd --reload
 查看 开启端口 firewall-cmd --list-all
 lsof -i:15672

```



```
http://192.168.2.10:15672  管控台    5672 (java 通信端口号)25672  集群 通信端口号
```



# 命令行与管控台操作

```
rabbitmqctl stop_v rabbitmgctl stop app:关闭应用
rabbitmgctl start app:启动应用
rabbitmgctl status:节点状态
rabbitmgctl add user username password:添加用户
rabbitmgctl list users:列出所有用户
rabbitmgctl delete user username:删除用户
rabbitmgctl clear permissions-pⅦ hostpath username:清除用户权限
rabbitmgctl list user permissions username:列出用户权限
rabbitmgctl change password username newpassword:修改密码
rabbitmqctl set permissions-p Vhostpath username
*"".*""":设置用户权限
rabbitmgctl add vhost vhostpath:创建虚拟主机
rabbitmgctl list vhosts:列出所有虚拟主机
rabbitmgctl list permissions-pⅦ hostpath:列出虚拟主机上所有权限
rabbitmgctl delete vhost vhostpath:删除虚拟主机
rabbitmgcti list queues:查看所有队列信息
rabbitmgct- o vhostpath purge queue blue:清除队列里的消息

rabbitmgctl reset:移除所有数据,要在 abbitmgctI stop app之后使用
rabbitmgctl join cluster< clusternode>[-ram:组成集群命令
rabbitmgctl cluster status:查看集群状态

rabbitmqctl change cluster node type disc ram
修改集群节点的存储形式
rabbitmgctl forget cluster node[- offline忘记节点(摘除节点)
rabbitmgctl rename cluster node oldnode1 newnode 1 [oldnode2
[ newnode2.](修改节点名称)
```





交换机属性
Name:交换机名称
Type∶交换机类型 direct、 topIC、 fanout、 headers
Durability:是否需要持久化,true为持久化

Auto delete:当最后一个绑定到 Exchange上的队列删除后,自动删除该 Exchange
Internal:当前 Exchange是否用于 RabbitMO内部使用,默认为 False(不使用)
Arguments:扩展参数,用于扩展AMQP协议自制定化使用

![image-20200404100749527](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\3.png)

![image-20200404102640175](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404102640175.png)

![image-20200404102708482](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404102708482.png)

![image-20200404103054320](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404103054320.png)

![image-20200404104557169](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404104557169.png)

![image-20200404105335135](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404105335135.png)

![image-20200404110156115](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404110156115.png)

Direct Exchange
所有发送到 Direct Exchange的消息被转发到 RouteKey中指定的 Queue
注意: Direct模式可以使用 RabbitMQ自带的 Exchange: default
Exchange,所以不需要将 Exchange进行任何绑定( binding)操作,消息传
递时, Routekey必须完全匹配才会被队列接收,否则该消息会被抛弃

交换机主要包括如下4种类型：

1. Direct exchange（直连交换机）
2. Fanout exchange（扇型交换机）
3. Topic exchange（主题交换机）
4. Headers exchange（头交换机）



![image-20200402114720445](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200402114720445.png)

![image-20200402114952722](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\2.png)





![image-20200404110951717](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404110951717.png)





![image-20200404112248500](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404112248500.png)

![image-20200404112314862](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404112314862.png)



![image-20200404112546167](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404112546167.png)







![image-20200404113352094](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404113352094.png)





![image-20200404113856672](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404113856672.png)



![image-20200404114044695](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404114044695.png)



![image-20200404114628990](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404114628990.png)





![image-20200404114944526](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404114944526.png)





![image-20200404115019253](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404115019253.png)



![image-20200404115121246](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404115121246.png)



![image-20200404120138153](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404120138153.png)



![image-20200404120218222](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404120218222.png)





![image-20200404120237868](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404120237868.png)



![image-20200404122538522](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404122538522.png)





![image-20200404122603392](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404122603392.png)



![image-20200404122633202](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404122633202.png)



![image-20200404122658019](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404122658019.png)





![image-20200404122859010](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404122859010.png)



![image-20200404122934999](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404122934999.png)



![image-20200404123044829](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404123044829.png)



![image-20200404123947243](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404123947243.png)





![image-20200404124105424](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404124105424.png)





![image-20200404124151714](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404124151714.png)





![image-20200404124233005](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404124233005.png)





![image-20200404124342791](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404124342791.png)



![image-20200404130152850](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404130152850.png)



![image-20200404130221723](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404130221723.png)





![image-20200404130421915](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404130421915.png)



![image-20200404130451805](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404130451805.png)



















































































![image-20200404131748315](E:\Users\kai.xu\Desktop\My\Typora-Note\image\RabbitMQ\image-20200404131748315.png)



























































































































































