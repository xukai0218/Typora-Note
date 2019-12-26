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





























