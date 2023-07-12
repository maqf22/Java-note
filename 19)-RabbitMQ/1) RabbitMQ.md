
# 一、MQ概述

> Message Queue - 消息队列，是消息在传输过程中进行保存的容器，用于分布式系统之间的通信

![[Pasted image 20230712153224.png]]

- 优势
	- 应用解耦：高内聚、低耦合，可提高可维护性
	- 异步提速：提升用户的吞吐量（单位时间内处理请求的数量）
	- 峭峰填谷：降低业务处理服务的峰值请求数量，提高服务处理的稳定性
- 劣势
	- 系统可用性低：系统引入的外部依赖越多，系统的稳定性就越差，一旦MQ宕机，同样会对业务造成影响
	- 系统复杂度高：使用的技术越多，复杂性越高。如何确保重复消费？消息丢失？消息顺序？
	- 一致性：通过MQ向多个服务发送消息，不一定的有的服务都会处理成功，如何保证一致性

> 适合使用MQ的场景
> 1. 生产者不需要从消费者处获得反馈
> 2. 可以接受短暂的不一致性
> 3. 有效的体现优势


# 二、RabbitMQ概述

## 1. AMQP

> Advanced Message Queuing Protocol - 高级消息队列协议，也是一个网传输协议，是应用层协议的一个开发标准，是面向消息的中间件设计，基于此协议的客户端与消息中间件可以传递消息。

## 2. RabbitMQ相关概念

### 1) 名词解释

名词 | 说明
:- | :-
Broker | 接收和分消息的应用，RabbitMQ Server就是Message Broker
Virtual host | 出于多租户和安全素设计的，把AMQP的基本组件划分到一个虚拟的分组中，类似于网中的namespace概念。当多个不同的用户使用同一个RabbitMQ server提供的服务时，可以划分出多个vhost，每个用户在自己的vhost创建exchange queue等
Connection | publisher consumer和broker之间的TCP连接
Channel | 如果每一次访问RabbitMQ都建立一个Connection，在消息量大的时候建立TCP Connection的开销将是巨大的，效率也较低。Channel是在connetion内部建立的逻辑连接，如果应用程序支持多线程，通常每个thread创建单独的channel进行通讯，AMQP method包含了channel id帮助客户端和message borker识别channel,所以channel之间是完全隔离的。Channel作为轻量级Connection极大减少操作系统建立TCP connection的开销
Exchange | message到达broker的第一站，根据分发规则，匹配查询表中的routing key，分发消息到queue中去。常用的类型有：direct(point to point), topic(publish subscribe) and fanout(multicast)
Queue | 消息最终被送到这里等待consumer取走
Binding | exchange和queue之间的虚拟连接，binding中可以包含routing key。Binding信息保存到exchange中的查询表中，用于message的分发依据

### 2) 工作模式

![[Pasted image 20230712160450.png]]

#### a. simple简单模式

![[Pasted image 20230712160825.png]]

1. 消息产生者将消息放入队列
2. 消息的消费者(consumer)监听(while)消息队列，如果队列中有消息就消费掉，消息被拿走后自动从队列中删除（隐患消息可能没有被消费者正确处理，已经从队列中消失了，造成消息的丢失）应用场景：聊天（中间有一个过度的服务器：p端, c端）

#### b. work工作模式（资源的竞争）

![[Pasted image 20230712161246.png]]

1. 消息产者将消息放入队列消费者可以有多个，消费者1，消费者2，同时监听同一个队列，消息被消费？C1 C2共同争抢当前的消息队列内容，谁先拿到谁负责消费消息（隐患，高并发情况下，默认会产生某一个消息被多个消费者共同使用，可以设置一个开关(syncronize，与同步锁的性能不一样)保证一条消息只能被一个消费者使用）
2. 应用场景：红包；大项目中的资源调度（任务分配不需知道哪一个任务执行系统在空闲，直接将任务扔到消息队列中，空闲的系统自动争抢）

#### c. publish/subscribe发布订阅（共享资源）

![[Pasted image 20230712161848.png]]

1. X代表交换机RabbitMQ内部组件，erlang消息产生者是代码完成，代码的执行效率不高，消息产生者将消息放入交换机，交换机发布订阅把消息发送到所有消息队列中，对应消息队列的消费者拿到消息进行消费
2. 相关场景：邮件群发，群聊天，广播（广告）

#### d. routing路由模式

![[Pasted image 20230712162148.png]]

1. 消息生产者将消息发送给交换机按照路由判断，路由是字符串(info)当前产生的消息携带路由字符（对象的方法），交换机根据路由的key，只能匹配上路由key对应的消息队列，对应的消费者才能消费消息
2. 根据业务功能定义路由字符串
3. 从系统的代码逻辑中获取对应的功能字符串，将消息任务扔到对应的队列中业务景：error通知；exception错误通知的功能，传统意义的错误通知，客户通知，利用key路由，可以将程序中的错误封装成消息传入到消息队列中，开发者可以自定义消费者，实时接收错误

#### e. topic主题模式（路由模式的一种）

![[Pasted image 20230712162713.png]]

1. 星号进号代表通配符
2. 星号代表多个单词，井号代表一个单词
3. 路由功能添加模糊匹配
4. 消息产生者产生消息，把消息交给交换机
5. 交换机根据key的规则模糊匹配到对应的队列，由队列的监听消费者接收消息消费

> [!TIP]
> 具体详见官网：https://www.rabbitmq.com/getstarted.html

## 3. JMS

JMS即Java消息服务（Java Message Service）应用程序接口，是一个Java平台中关于面向消息中间件的API；
JMS是JavaEE规范中的一种，类比JDBC；
很多消息中间件都实件了JMS规范，RabbitMQ官方没有提供JMS的实现包，但是开源社区有；


# 三、RabbitMQ安装与配置

官网：https://www.rabbitmq.com/
环境准备
- CentOS-7
- otp_src_19.3.tar.gz：erLang语言支持 `https://github.com/erlang/otp/releases?page=25`
- rabbitmq-server-3.7.2-1.el7.noarch.rpm：RabbitMQ服 `https://github.com/rabbitmq/rabbitmq-server/releases?page=24`

## 1. CentOS-7安装

系统安装略

安装ifconfig命令
1. 输入命令dhclient，可以自动获取一个IP地址，再用命令ip -a addr查看IP
2. 然后输入yum search ifconfig查找符合这个命令的组件，查找到net-tools.x86_64，安装这个组件
3. yum install net-tools.x86_64
4. 安装成功后输入ifconfig测试
5. 安装必要的软件 `yum install wget vim unzip net-tools -y`
6. 关闭防火墙
```
systemctl disable firewalld
systemctl stop firewalld
```
7. 关闭`SELiunx`  `vi /etc/seliunx/config`，修改配置文件保存后退出

## 2. Windows Terminal的SSH连接

**步骤**





