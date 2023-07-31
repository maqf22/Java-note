
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
- otp_src_19.3.tar.gz：erLang语言支持 `https://github.com/erlang/otp/releases?page=25 (http://www.erlang.org/download/otp_src_19.3.tar.gz)`
- rabbitmq-server-3.7.2-1.el7.noarch.rpm：RabbitMQ服 `https://github.com/rabbitmq/rabbitmq-server/releases?page=24 (https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.7.2/rabbitmq-server-3.7.2-1.el7.noarch.rpm)`

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

1. 打开Windows Terminal，点击下图位置设置

![[Pasted image 20230713134927.png]]

2. 在使用任一文本编辑器打开setting.json文件
![[Pasted image 20230713135058.png]]

3. Ctrl + F 搜索profiles，跳转到下图所在代码块
![[Pasted image 20230713141308.png]]

4. 添加一个新的List选项
```json
{
	"guid": "{这里需要生成一个自己的GUID}",
	"hidden": false,
	"name": "列表选项名"，
	"commandline": "ssh root@192.168.10.128"
}
```

5. 在https://www.guidgen.com网站生成唯一的guid
![[Pasted image 20230713140135.png]]

6. 将刚刚生成的GUID填写到新的List选项中，保存退出即可

7. 在Windows Terminal直接打开使用即可

> 修改`/etc/ssh/sshd_config`配置文件`ClientAliveInterval 60`避免自动断开连接

## 3. RabbitMQ安装

1. 安装必要的依赖
`yum install gcc glibc-devel make ncurses-devel openssl-devel xmlto -y`

2. 用你喜欢的方式将安装包放到Liunx的家目录里

3. 编写Shell脚本如下
```shell
#!/bin/bash
# setup erLang & RabbitMQ
tar -zxvf otp_src_19.3.tar.gz
mkdir /usr/local/erlang
cd otp_src_19.3
./configure --prefix=/usr/local/erlang --without-javac
make && make install
echo 'ERL_HOME=/usr/local/erlang'>>/etc/profile
echo 'PAHT=$ERL_HOME/bin:$PATH'>>/etc/profile
echo 'export ERL_HOME PATH'>>/etc/porfile

cd ~
rpm -ivh --nodeps rabbitmq-server-3.7.2-1.el7.noarch.rpm

# source命令只在当前的shell脚本当中生效，在新的脚本中没有作用
source /etc/profile
```

4. 编写配置文件`vi /etc/rabbitmq/rabbitmq-env.conf`添加内容（此文件默认不存在）
```conf
NODENAME=rabbit@localhost
```

5. 启动查看rabbitmq-server
`rabbitmq-server start &`


# 四、RabbitMQ常用命令

## 1. 启动与关闭

### 1) 启动RabbitMQ
```
rabbitmq-server start &
```

> [!注意]
> 1. 这里可能会出现错误，错误原因是/var/lib/rabbitmq/.erlang.cookie文件权限不够。
> 	解决方案：对这个文件授权，在任意处敲下面两条命令
> 	`chown rabbitmq:rabbitmq /var/lib/rabbitmq/.erlang.cookie`
> 	`chmod 400 /var/lib/rabbitmq/.erlang.cookie`
> 2. 如果启动速度过慢
> 	解决方案：修改本地IP映射文件`/etc/hosts`添加本地IP地址对应`localhost`；**注意：**修改之后需要重启系统生效

### 2) 停止服务

```
rabbitmqctl stop
```

### 3) 查看服务启动状态

```
ps -ef | grep rabbitmq
```

## 2. 插件管理

1. 添加插件`rabbitmq-plugins enable {插件名}

2. 删除插件`rabbitmq-plugins disable {插件名}`

> [!注意]
> RabbitMQ启动以后可以使用浏览器进入管控台，但是默认情况RabbitMQ不允许直接使用浏览器进行访问。因此必须添加插件：`rabbitmq-plugins enalbe rabbitmq_management`

3. 使用浏览器访问管理控台`http://RabbitMQ` 服务器端口：15672 `http://192.168.10.128:15672`

	**管理控制台的使用**
	
	1. 激活管理插件 - `rabbitmq-plugins enable rabbitmq_management`
	2. 启动RabbitMQ - `rabbitmq-server start &`

4. 使用浏览器访问 `http://192.168.10.128:15672`

5. 添加一管理员用户 `rabbitmqctl add_user root root`

6. 添加用户角色 - `rabbitmqctl set_user_tags root administrator`

7. 使用用户名和密码登录

## 3. 用户管理

RabbitMQ安装成功后使用默认用户名guest登录
账号：guest
密码：guest
注意：这里guest只允许本机登录访问需要创建用户并授权远程访问命令如下

1. 添加用户
	- 语法：`rabbitmqctl add_user {username} {password}`
	- 案例：`rabbitmqctl add_user root root`
2. 删除用户：
	- 语法：`rabbitmqctl delete_user {username}`
	- 案例：`rabbitmqctl delete_user maqf`
3. 修改密码
	- 语法：`rabbitmqctl change_password {username} {newpassword}`
	- 案例：`rabbitmqctl change_password root 123`
4. 设置用户色：
	- 语法：`rabbitmqctl set_user_tags {username} {tag}`
	- 案例：`rabbitmqctl set_user_tags root administrator`
	- tag为用户角色，详见下表

角色 | 说明
:- | :-
management | 用户可以通过AMQP做的任何事外加：列出自己可以通过AMQP登入的virtual hosts，查看自己的virtual hosts中的queues, exchanges和bindings，查看和关闭自己的channels和connections，查看有关自己的virtual hosts的“全局”的统计信息，包含其他用户在这些virtual hosts中的活动
policymaker | management可以做的任何事外加：查看、创建和删除自己的virtual hosts所属的policies和parameters
monitoring | management可以做的任何事外加：列出所有virtual hosts，包括他们不能登录的virtual hosts，查看其他用户的connections和channels，查看节点级别的数据如clustering和memory使用情况，查看真正的关于所有virtual hosts的全局的统计信息
administrator | policymaker和monitoring可以做的任何事外加：创建和删除virtual hosts，查看、创建和删除users，查看创建和删除permissions，关闭其他用户的connections

5. 通过管控台管理用户

![[Pasted image 20230713161829.png]]

## 4. 权限管理

1. 授权命令：
	- 语法：`rabbitmqctl set_permissions [-p vhostpath] {user} {conf} {write} {read}`
		- `-p vhostpath`：用于指定一个资源的命名空间，例如 -p / 表示根路径命名空间
		- `user`：用于指定要为哪个用户授权填写用户名
		- `conf`：一个正则表达式match哪些配置资源能够被该用户配置
		- `write`：一个正则表达式match哪些配置资源能够被该用户写
		- `read`：一个正则表达式match哪此配置资源能够被该用户访问
	- 案例：`rabbitmqctl set_permissions -p / root '.*' '.*' '.*'` （用于设置root用户拥有对所有资源的读写配置权限）
2. 查看用户权限：
	- 语法：`rabbitmqctl list_permissions [vhostpath]`
	- 案例：
		- `rabbitmqctl list_permissions` 查看根路径下的所有用户权限
		- `rabbitmqctl list_permissions /abc` 查看指定命名空间下的所有用户权限
3. 查看指定用户下的权限：
	- 语法：`rabbitmqctl list_user_permissions {username}`
	- 案例：`rabbitmqctl list_user_permissions root` 查看root用户下的权限
4. 清除用户权限：
	- 语法：`rabbitmqctl clear_permissions {username}`
	- 案例：`rabbitmqctl clear_permissions root` 清除root用户的权限
5. 管控台操作：
![[Pasted image 20230713163537.png]]

## 5. vhost管理

vhost是RabbitMQ中的一个命名空间，可以限制消息的存放位置利用这个命名空间可以进行权限的控制，有点类似Windows中的文件夹一样，在不同的文件夹中存放不同的文件

1. 添加vhost：
	- 语法：`rabbitmqctl add vhost {name}`
	- 案例：`rabbitmqctl add vhost maqf`
2. 删除vhost：
	- 语法：`rabbitmqctl delete vhost {name}`
	- 案例：`rabbitmqctl delete vhost maqf`
3. 管控台操作：
![[Pasted image 20230713164219.png]]


# 五、RabbitMQ消息收发

所有MQ产品从模型抽象角度来讲，都是一样的过程，消费者(Consumer)订阅某个队列，生产者(Provicer)创建消息，然后发布到队列(Queue)中，最后将消息发送到监听的消息者

![[Pasted image 20230713165333.png]]

![[Pasted image 20230713165346.png]]

生产者将消息发送到Broker（用于消息收发的应用）
在Broker中有Virual Host（命名空间）
Virtual Host中有Exchange（交换机）
当Exchange接收到消息之后，直接返回生产者
Exchange最终会将消息存入Queue（队列）
通过Binding（路由表，一个N行三列的表格结构）将消息存入到指定的队列
当消息进入到Queue之后实现持久化存储
Exchange不负责消息存储，就是个中转站
消费者通过Connection（连接通道）来对队列中的消息进行消费
如果队列中有消息，直接被拿走消费，如果没有，等消息来再消费

相关概念

名词 | 说明
:- | :-
Message | 消息，消息是不具体的，它由消息冰龙和消息体组成，消息体是不透明的，而消息头则由一系列的可选属性组成，这些属性包括：routing-key（路由键）、priority（相对于其他消息的优先权）、delivery-mode（指出该消息可能需要持久性存储）等
Publisher | 消息的生产者，也是一个向交换器发布消息的客户端应用程序
Exchange | 交换器，用来接收生产者发送的消息并将这些消息路由给服务器中的队列


# 六、Exchange类型

## 1. direct

消息中的路由键（routing key）如果和Binding中的binding key一致，交换器就将消息发到对应的队列中。路由键与队列名完全匹配，如果一个队列绑定到交换机要求路由键为"dog"，则只转发routing key标记为"dog"的消息，不会转发"dog.puppy"，也不会转发"dog.guard"等等。它是完全匹配、单播的模式

![[Pasted image 20230713170755.png]]

## 2. fanout

每个发到fanout类型交换器的消息都会分到所有绑定的队列上去。fanout交换器不处理路由键，只是简单的将队列绑定到交换器上，每个发送到交换器的消息都会被转发到与该交换器绑定的所有队列上。很像广播，每台子网内的主机都获得了一份复制的消息。fanout类型转发消息是最快的

![[Pasted image 20230713171239.png]]

## 3. topic

topic（多用于定于订阅模式）交换器通过模式匹配分配消息的路由键属性，将路由键和某个模式进行匹配，此时队列需要绑定到一个模式上。它将路由键和绑定键的字符串切分成单词，这些单词之间用点隔开。它同样也会识别两个通配符：符号“#”符号“*”。# 匹配0个或多个单词，\* 匹配不多不少一个单词

![[Pasted image 20230713171820.png]]


# 七、快速上手

## 1. 向MQ发送消息

> 不使用Exchange交换器，向MQ发送消息

1. 新建Maven模块MQ-01-Send

2. 添加依赖`pom.xml`
```xml
<dependencies>
	<dependency>
		<groupId>com.rabbitmq</groupId>
		<artifactId>amqp-client</artifactId>
		<version>5.14.2</version>
	</dependency>
</dependencies>
```

3. 编写发送代码`Send.java`
```java
package com.example;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Send {
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂，用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128"); // IP地址
        factory.setPort(5672); // Broker端口
        factory.setUsername("root"); // 用户名
        factory.setPassword("root"); // 密码

        // 新建连接（也可内部处理异常）
        Connection connection = factory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();

        /*
         * 声明队列
         * 参数一：队列名（自定义），如果队列名不存在则创建，存在则放弃
         * 参数二：是否支持持久化
         * 参数三：是否排外，true表示排外，如果消费者监听了这个队列则不允许其他消费者监听此队列
         * 参数四：是否删除，true表示自动删除，如果没有消息者监听这个队列则自动删除
         * 参数五：队列的属性设置，通常设置为null
         * */
        String queueName = "myQueue";
        channel.queueDeclare(queueName, true, false, false, null);

        /*
         * 发送消息到MQ
         * 参数一：交换机名，不使用交换机则填写空串""
         * 参数二：消息所携带的RoutingKey，不使用交换机，此参数会被识别成队列名
         * 参数三：消息的属性，通常设置为null
         * 参数四：具体消息数据取值，byte[]类型
         * */
        String message = "Test message !~"; // 需要发送的消息，通常使用字符串
        channel.basicPublish("", queueName, null, message.getBytes());

        System.out.println("消息发送成功~");

        // 释放资源
        // 选释放通道资源
        if (null != channel)
            channel.close();
        // 再释放连接资源
        if (null != connection)
            connection.close();
    }
}
```

## 2. 从MQ接收消息

1. Copy之前的Send模块，并做一些常规修改

2. 编写接收代码
```java
package com.example;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Receive {
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂，用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128"); // IP地址
        factory.setPort(5672); // Broker端口
        factory.setUsername("root"); // 用户名
        factory.setPassword("root"); // 密码

        // 新建连接（也可内部处理异常）
        Connection connection = factory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();

        /*
         * 声明队列
         * 参数一：队列名（自定义），如果队列名不存在则创建，存在则放弃
         * 参数二：是否支持持久化
         * 参数三：是否排外，true表示排外，如果消费者监听了这个队列，则不允许其他消费者监听此队列
         * 参数四：是否删除，true表示自动删除，如果没有消息者监听这个队列则自动删除
         * 参数五：队列的属性设置，通常设置为null
         * */
        String queueName = "myQueue";
        channel.queueDeclare(queueName, true, false, false, null);

        // 接收代码：
        /*
         * 从MQ接收消息
         * 参数一：队列名称
         * 参数二：是否自动确认消息，true表示自动确认，当消接收后无论是否正确完成处理，都会自动从列表中称除
         * 参数三：消息处理的回调方法
         * 注意：
         * basicConsume方法会在底层启动一个子线程，用于持续监听队列消息，所以不能关闭
         * */
        channel.basicConsume(queueName, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                // super.handleDelivery(consumerTag, envelope, properties, body);
                String message = new String(body);
                System.out.println("接收到了 => " + message);
            }
        });
    }
}
```

3. 运行测试接收


# 八、基于Exchange的收发

## 1. 基于direct的收发

1. Copy发送模块，并做必要的修改

2. 编写发送代码
```java
package com.example;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class DirectSend {
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂，用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128"); // IP地址
        factory.setPort(5672); // Broker端口
        factory.setUsername("root"); // 用户名
        factory.setPassword("root"); // 密码

        // 新建连接（也可内部处理异常）
        Connection connection = factory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();

        /*
         * 声明队列
         * 参数一：队列名（自定义），如果队列名不存在则创建，存在则放弃
         * 参数二：是否支持持久化
         * 参数三：是否排外，true表示排外，如果消费者监听了这个队列，则不允许其他消费者监听此队列
         * 参数四：是否删除，true表示自动删除，如果没有消息者监听这个队列则自动删除
         * 参数五：队列的属性设置，通常设置为null
         * */
        String queueName = "directQueue";
        channel.queueDeclare(queueName, true, false, false, null);

        /*
        * 声明交换机
        * 参数一：交换机名称，如果不存在则创建，存在则放弃
        * 参数二：交换机类型，取值为：direct fanout topic headers
        * 参数三：是否持久化
        * */
        String exchangeName = "directExchange";
        channel.exchangeDeclare(exchangeName, "direct", true);
        /*
        * 将队列和交换机绑定
        * 参数一：队列名
        * 参数二：交换机名
        * 参数三：绑定时的BindingKey
        * */
        channel.queueBind(queueName, exchangeName, "directKey");

        /*
         * 发送消息到MQ
         * 参数一：交换机名，不使用交换机则填写空串""
         * 参数二：消息所携带的RoutingKey，不使用交换机，此参数会被识别成队列名
         * 参数三：消息的属性，通常设置为null
         * 参数四：具体消息数据取值，byte[]类型
         * */
        String message = "Test directed message !~"; // 需要发送的消息，通常使用字符串
        // 发送时 参数一、参数二，使用具体的交换机名称和BindingKey
        channel.basicPublish(exchangeName, "directKey", null, message.getBytes());

        System.out.println("消息发送成功~");

        // 释放资源
        // 选释放通道资源
        if (null != channel)
            channel.close();
        // 再释放连接资源
        if (null != connection)
            connection.close();
    }
}
```

3. 运行测试发送

4. Copy发送模块，并做必要的修改

5. 编写接收代码
```java
package com.example;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class DirectReceive {
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂，用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128"); // IP地址
        factory.setPort(5672); // Broker端口
        factory.setUsername("root"); // 用户名
        factory.setPassword("root"); // 密码

        // 新建连接（也可内部处理异常）
        Connection connection = factory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();

        /*
         * 声明队列
         * 参数一：队列名（自定义），如果队列名不存在则创建，存在则放弃
         * 参数二：是否支持持久化
         * 参数三：是否排外，true表示排外，如果消费者监听了这个队列，则不允许其他消费者监听此队列
         * 参数四：是否删除，true表示自动删除，如果没有消息者监听这个队列则自动删除
         * 参数五：队列的属性设置，通常设置为null
         * */
        String queueName = "directQueue";
        channel.queueDeclare(queueName, true, false, false, null);

        /*
         * 声明交换机
         * 参数一：交换机名称，如果不存在则创建，存在则放弃
         * 参数二：交换机类型，取值为：direct fanout topic headers
         * 参数三：是否持久化
         * */
        String exchangeName = "directExchange";
        channel.exchangeDeclare(exchangeName, "direct", true);
        /*
         * 将队列和交换机绑定
         * 参数一：队列名
         * 参数二：交换机名
         * 参数三：绑定时的BindingKey
         * */
        channel.queueBind(queueName, exchangeName, "directKey");

        // 接收代码：
        /*
         * 从MQ接收消息
         * 参数一：队列名称
         * 参数二：是否自动确认消息，true表示自动确认，当消接收后无论是否正确完成处理，都会自动从列表中称除
         * 参数三：消息处理的回调方法
         * 注意：
         * basicConsume方法会在底层启动一个子线程，用于持续监听队列消息，所以不能关闭
         * */
        channel.basicConsume(queueName, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                // super.handleDelivery(consumerTag, envelope, properties, body);
                String message = new String(body);
                System.out.println("接收到了Direct => " + message);
            }
        });
    }
}
```

6. 运行测试接收

## 2. 基于fanout的收发

1. 编写接收端代码（多个接收端）
```java
package com.example;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class FanoutReceive01 {
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂，用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128"); // IP地址
        factory.setPort(5672); // Broker端口
        factory.setUsername("root"); // 用户名
        factory.setPassword("root"); // 密码

        // 新建连接（也可内部处理异常）
        Connection connection = factory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();

        /*
         * 声明队列
         * 参数一：队列名（自定义），如果队列名不存在则创建，存在则放弃
         * 参数二：是否支持持久化
         * 参数三：是否排外，true表示排外，如果消费者监听了这个队列，则不允许其他消费者监听此队列
         * 参数四：是否删除，true表示自动删除，如果没有消息者监听这个队列则自动删除
         * 参数五：队列的属性设置，通常设置为null
         * */
        // String queueName = UUID.randomUUID().toString();
        // 设置第二个参数false - 持久化，第三个参数true - 排外，第四个参数true - 自动删除
        // channel.queueDeclare(queueName, false, true, true, null);

        // 等价于上面两行
        String queueName = channel.queueDeclare().getQueue();

        /*
         * 声明交换机
         * 参数一：交换机名称，如果不存在则创建，存在则放弃
         * 参数二：交换机类型，取值为：direct fanout topic headers
         * 参数三：是否持久化
         * */
        String exchangeName = "fanoutExchange";
        channel.exchangeDeclare(exchangeName, "fanout", true);
        /*
         * 将队列和交换机绑定
         * 参数一：队列名
         * 参数二：交换机名
         * 参数三：绑定时的BindingKey
         * */
        channel.queueBind(queueName, exchangeName, "");

        // 接收代码：
        /*
         * 从MQ接收消息
         * 参数一：队列名称
         * 参数二：是否自动确认消息，true表示自动确认，当消接收后无论是否正确完成处理，都会自动从列表中称除
         * 参数三：消息处理的回调方法
         * 注意：
         * basicConsume方法会在底层启动一个子线程，用于持续监听队列消息，所以不能关闭
         * */
        channel.basicConsume(queueName, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                // super.handleDelivery(consumerTag, envelope, properties, body);
                String message = new String(body);
                System.out.println("接收到了Fanout => " + message);
            }
        });
    }
}
```

2. 编写发送端
```java
package com.example;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class FanoutSend {
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂，用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128"); // IP地址
        factory.setPort(5672); // Broker端口
        factory.setUsername("root"); // 用户名
        factory.setPassword("root"); // 密码

        // 新建连接（也可内部处理异常）
        Connection connection = factory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();

        // 声明队列（不需要创建队列）
        // String queueName = "directQueue";
        // channel.queueDeclare(queueName, true, false, false, null);

        /*
         * 声明交换机
         * 参数一：交换机名称，如果不存在则创建，存在则放弃
         * 参数二：交换机类型，取值为：direct fanout topic headers
         * 参数三：是否持久化
         * */
        String exchangeName = "fanoutExchange";
        channel.exchangeDeclare(exchangeName, "fanout", true);

        // 将队列和交换机绑定（不需要绑定，因不知道发给谁）
        // channel.queueBind(queueName, exchangeName, "");

        /*
         * 发送消息到MQ
         * 参数一：交换机名，不使用交换机则填写空串""
         * 参数二：消息所携带的RoutingKey，不使用交换机，此参数会被识别成队列名
         * 参数三：消息的属性，通常设置为null
         * 参数四：具体消息数据取值，byte[]类型
         * */
        String message = "Test fanout message !~"; // 需要发送的消息，通常使用字符串
        // 因为是Fanout类型，不知道发给谁，所以不需要RoutingKey
        channel.basicPublish(exchangeName, "", null, message.getBytes());

        System.out.println("消息发送成功~");

        // 释放资源
        // 选释放通道资源
        if (null != channel)
            channel.close();
        // 再释放连接资源
        if (null != connection)
            connection.close();
    }
}
```

3. 运行接收端和发送端进测试

## 3. 基于topic的收发

1. 编写接收端代码（多个）
```java
package com.example;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class TopicReceive01 {
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂，用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128"); // IP地址
        factory.setPort(5672); // Broker端口
        factory.setUsername("root"); // 用户名
        factory.setPassword("root"); // 密码

        // 新建连接（也可内部处理异常）
        Connection connection = factory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();

        /*
         * 声明队列
         * 参数一：队列名（自定义），如果队列名不存在则创建，存在则放弃
         * 参数二：是否支持持久化
         * 参数三：是否排外，true表示排外，如果消费者监听了这个队列，则不允许其他消费者监听此队列
         * 参数四：是否删除，true表示自动删除，如果没有消息者监听这个队列则自动删除
         * 参数五：队列的属性设置，通常设置为null
         * */
        // String queueName = UUID.randomUUID().toString();
        // 设置第二个参数false - 持久化，第三个参数true - 排外，第四个参数true - 自动删除
        // channel.queueDeclare(queueName, false, true, true, null);

        // 等价于上面两行
        String queueName = channel.queueDeclare().getQueue();

        /*
         * 声明交换机
         * 参数一：交换机名称，如果不存在则创建，存在则放弃
         * 参数二：交换机类型，取值为：direct fanout topic headers
         * 参数三：是否持久化
         * */
        String exchangeName = "topicExchange";
        channel.exchangeDeclare(exchangeName, "topic", true);

        /*
         * 将队列和交换机绑定
         * 参数一：队列名
         * 参数二：交换机名
         * 参数三：绑定时的BindingKey
         * topic可以使用通配符 * 或 #
         * * 表示必须要匹配一个任意单词
         * # 表示可以匹配任意个任意单词
         * 多个单词之间可以使用点儿将其进行分隔，例如：it.ma.qf 或者 it.*
         * */
        channel.queueBind(queueName, exchangeName, "it");
        // channel.queueBind(queueName, exchangeName, "it.*");
        // channel.queueBind(queueName, exchangeName, "it.#");

        // 接收代码：
        /*
         * 从MQ接收消息
         * 参数一：队列名称
         * 参数二：是否自动确认消息，true表示自动确认，当消接收后无论是否正确完成处理，都会自动从列表中称除
         * 参数三：消息处理的回调方法
         * 注意：
         * basicConsume方法会在底层启动一个子线程，用于持续监听队列消息，所以不能关闭
         * */
        channel.basicConsume(queueName, true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                // super.handleDelivery(consumerTag, envelope, properties, body);
                String message = new String(body);
                System.out.println("接收到了Topic => " + message);
            }
        });
    }
}
```

2. 编写发送端代码
```java
package com.example;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class TopicSend {
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂，用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128"); // IP地址
        factory.setPort(5672); // Broker端口
        factory.setUsername("root"); // 用户名
        factory.setPassword("root"); // 密码

        // 新建连接（也可内部处理异常）
        Connection connection = factory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();

        // 声明队列（不需要创建队列）
        // String queueName = "directQueue";
        // channel.queueDeclare(queueName, true, false, false, null);

        /*
         * 声明交换机
         * 参数一：交换机名称，如果不存在则创建，存在则放弃
         * 参数二：交换机类型，取值为：direct fanout topic headers
         * 参数三：是否持久化
         * */
        String exchangeName = "topicExchange";
        channel.exchangeDeclare(exchangeName, "topic", true);

        // 将队列和交换机绑定（不需要绑定，因不知道发给谁）
        // channel.queueBind(queueName, exchangeName, "");

        /*
         * 发送消息到MQ
         * 参数一：交换机名，不使用交换机则填写空串""
         * 参数二：消息所携带的RoutingKey，不使用交换机，此参数会被识别成队列名
         * 参数三：消息的属性，通常设置为null
         * 参数四：具体消息数据取值，byte[]类型
         * */
        // String routingKey = "it";
        // String routingKey = "it.a";
        String routingKey = "it.ab";
        String message = "Test topic message !~"; // 需要发送的消息，通常使用字符串
        // topic类型
        channel.basicPublish(exchangeName, routingKey, null, message.getBytes());

        System.out.println("消息发送成功~");

        // 释放资源
        // 选释放通道资源
        if (null != channel)
            channel.close();
        // 再释放连接资源
        if (null != connection)
            connection.close();
    }
}
```

3. 运行接收和发送端测试


# 九、事务消息

保证消息如果正确就全写入到MQ，不正确一旦出现问题就全都不写入MQ。其本质就是防止消息丢失。RabbitMQ中提供了两种方式来解决相关的问题：
1. 通过AMQP提供的事务机制来实现
2. 使用发送者确认模式来实现

## 1. 事务相关方法

方法 | 说明
:- | :-
`channel.txSelect()` | 声明启动事务模式
`channel.txCommit()` | 事务提交
`channel.txRollback()` | 事务回滚

> 带有事务的发送

```java
package com.example;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class TransactionSend {
    public static void main(String[] args) {
        // 创建连接工厂,用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128");
        factory.setPort(5672);
        factory.setUsername("root");
        factory.setPassword("root");

        // 新建连接
        Connection connection = null;
        Channel channel = null;
        try {
            connection = factory.newConnection();
            channel = connection.createChannel();

            // 声明队列
            String queueName = "transactionQueue";
            channel.queueDeclare(queueName, true, false, false, null);

            // 声明交换机
            String exchangeName = "transactionExchange";
            channel.exchangeDeclare(exchangeName, "direct", true);

            // 绑定交换机
            channel.queueBind(queueName, exchangeName, "transactionKey");

            // 定义消息数据
            String message = "Direct transaction test message !~";

            // 开启事务,必须要显示的调用txCommit()或者txRollback()
            channel.txSelect();
            // 发送时 参数一、参数二，使用具体的交换机名称和BindingKey
            channel.basicPublish(exchangeName, "transactionKey", null, message.getBytes());
            // 提交事务，当前通道的当前事务中的所有消息都写入到MQ中，并释放内存的记述当前事务
            channel.txCommit();

            System.out.println("发送成功~");
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        } finally {
            // 先释放通道资源
            if (null != channel) {
                try {
                    // 事务回滚，将当前通道中所有没有提交的消息全部删除并释放内存资源结束当前事务
                    channel.txRollback();
                    channel.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                } catch (TimeoutException e) {
                    throw new RuntimeException(e);
                }
            }
            // 再释放连接资源
            if (null != connection) {
                try {
                    connection.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```

## 2. 发送确认方式

### 1) waitForConfirms

```java
package com.example;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class ConfirmSend {
    public static void main(String[] args) {
        // 创建连接工厂,用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128");
        factory.setPort(5672);
        factory.setUsername("root");
        factory.setPassword("root");

        // 新建连接
        Connection connection = null;
        Channel channel = null;

        try {
            connection = factory.newConnection();
            channel = connection.createChannel();

            // 声明队列
            String queueName = "confirmQueue";
            channel.queueDeclare(queueName, true, false, false, null);

            // 声明交换机
            String exchangeName = "confirmExchange";
            channel.exchangeDeclare(exchangeName, "direct", true);

            // 绑定交换机
            channel.queueBind(queueName, exchangeName, "confirmKey");

            // 定义消息数据
            String message = "Direct confirm test message !~";

            // 开启发送确认模式
            channel.confirmSelect();

            // 发送时 参数一、参数二，使用具体的交换机名称和BindingKey
            channel.basicPublish(exchangeName, "confirmKey", null, message.getBytes());

            // 发送确认模式可能会造成MQ中的消息重复，消费者中需要拥有重复消息处理的能力
            try {
                // 这个方法会等待MQ返回确认信息，如果返回true表示成功，false表示失败
                // 这是一个阻塞时间，避免卡死，阻塞超时会抛出 TimeoutException
                if (!channel.waitForConfirms(5000L)) {
                    System.out.println("重新补发消息");
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
                // 如果出现异常也要补发消息
                System.out.println("重新补发消息");
            } catch (TimeoutException e) {
                // 如果出现超时也要补发消息
                System.out.println("重新补发消息");
            }

            System.out.println("发送成功~");
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        } finally {
            // 先释放通道资源
            if (null != channel) {
                try {
                    channel.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                } catch (TimeoutException e) {
                    throw new RuntimeException(e);
                }
            }
            // 再释放连接资源
            if (null != connection) {
                try {
                    connection.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```

### 2) waitForConfirmOrDie

```java
package com.example;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class ConfirmSend02 {
    public static void main(String[] args) {
        // 创建连接工厂,用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128");
        factory.setPort(5672);
        factory.setUsername("root");
        factory.setPassword("root");

        // 新建连接
        Connection connection = null;
        Channel channel = null;

        try {
            connection = factory.newConnection();
            channel = connection.createChannel();

            // 声明队列
            String queueName = "confirmQueue";
            channel.queueDeclare(queueName, true, false, false, null);

            // 声明交换机
            String exchangeName = "confirmExchange";
            channel.exchangeDeclare(exchangeName, "direct", true);

            // 绑定交换机
            channel.queueBind(queueName, exchangeName, "confirmKey");

            // 定义消息数据
            String message = "Direct confirm test message !~";

            // 开启发送确认模式
            channel.confirmSelect();

            // 发送时 参数一、参数二，使用具体的交换机名称和BindingKey
            channel.basicPublish(exchangeName, "confirmKey", null, message.getBytes());

            // 发送确认模式可能会造成MQ中的消息重复，消费者中需要拥有重复消息处理的能力
            try {
                // 这个方法没有返回值，如果正常执行结束则成功，如查出现异常则表示失败
                channel.waitForConfirmsOrDie(5000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
                // 如果出现异常也要补发消息
                System.out.println("重新补发消息");
            } catch (TimeoutException e) {
                // 如果出现超时也要补发消息
                System.out.println("重新补发消息");
            }

            System.out.println("发送成功~");
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        } finally {
            // 先释放通道资源
            if (null != channel) {
                try {
                    channel.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                } catch (TimeoutException e) {
                    throw new RuntimeException(e);
                }
            }
            // 再释放连接资源
            if (null != connection) {
                try {
                    connection.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```

### 3) addConfirmListener

```java
package com.example;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.ConfirmListener;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class ConfirmSend03 {
    public static void main(String[] args) {
        // 创建连接工厂,用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128");
        factory.setPort(5672);
        factory.setUsername("root");
        factory.setPassword("root");

        // 新建连接
        Connection connection = null;
        Channel channel = null;

        try {
            connection = factory.newConnection();
            channel = connection.createChannel();

            // 声明队列
            String queueName = "confirmQueue";
            channel.queueDeclare(queueName, true, false, false, null);

            // 声明交换机
            String exchangeName = "confirmExchange";
            channel.exchangeDeclare(exchangeName, "direct", true);

            // 绑定交换机
            channel.queueBind(queueName, exchangeName, "confirmKey");

            // 定义消息数据
            String message = "Direct confirm test message !~";

            // 开启发送确认模式
            channel.confirmSelect();

            // addConfirmListener
            channel.addConfirmListener(new ConfirmListener() {

                /**
                 * 消息发送成功之后要执行的回调方法
                 * @param l 消息编号
                 * @param b 消息是否批量确认
                 * @throws IOException
                 */
                @Override
                public void handleAck(long l, boolean b) throws IOException {
                    System.out.printf("消息成功发送，消息标号：%d，是否批量：%b\n", l, b);
                }

                /**
                 * 消息发送失败之后要执行的回调方法
                 * @param l 消息编号
                 * @param b 消息是否批量确认
                 * @throws IOException
                 */
                @Override
                public void handleNack(long l, boolean b) throws IOException {
                    System.out.printf("消息自己选择是否补发，消息标号：%d，是否批量：%b\n", l, b);
                }
            });

            for (int i = 0; i < 100; i++) {
                // 发送时 参数一、参数二，使用具体的交换机名称和BindingKey
                channel.basicPublish(exchangeName, "confirmKey", null, message.getBytes());
            }

            System.out.println("发送成功~");
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
        // addConfirmListener是异步的不要关掉通道和连接
        /*finally {
            // 先释放通道资源
            if (null != channel) {
                try {
                    channel.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                } catch (TimeoutException e) {
                    throw new RuntimeException(e);
                }
            }
            // 再释放连接资源
            if (null != connection) {
                try {
                    connection.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }*/
    }
}
```

### 4) 接收确认

```java
package com.example.receive;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class DirectReceive {
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂，用于指定RabbitMQ的连接信息
        ConnectionFactory factory = new ConnectionFactory();
        // 设置必要参数
        factory.setHost("192.168.10.128"); // IP地址
        factory.setPort(5672); // Broker端口
        factory.setUsername("root"); // 用户名
        factory.setPassword("root"); // 密码

        // 新建连接（也可内部处理异常）
        Connection connection = factory.newConnection();
        // 通过连接创建通道
        Channel channel = connection.createChannel();

        /*
         * 声明队列
         * 参数一：队列名（自定义），如果队列名不存在则创建，存在则放弃
         * 参数二：是否支持持久化
         * 参数三：是否排外，true表示排外，如果消费者监听了这个队列，则不允许其他消费者监听此队列
         * 参数四：是否删除，true表示自动删除，如果没有消息者监听这个队列则自动删除
         * 参数五：队列的属性设置，通常设置为null
         * */
        String queueName = "confirmQueue";
        channel.queueDeclare(queueName, true, false, false, null);

        /*
         * 声明交换机
         * 参数一：交换机名称，如果不存在则创建，存在则放弃
         * 参数二：交换机类型，取值为：direct fanout topic headers
         * 参数三：是否持久化
         * */
        String exchangeName = "confirmExchange";
        channel.exchangeDeclare(exchangeName, "direct", true);
        /*
         * 将队列和交换机绑定
         * 参数一：队列名
         * 参数二：交换机名
         * 参数三：绑定时的BindingKey
         * */
        channel.queueBind(queueName, exchangeName, "confirmKey");

        // 接收代码：
        /*
         * 从MQ接收消息
         * 参数一：队列名称
         * 参数二：是否自动确认消息，true表示自动确认，当消接收后无论是否正确完成处理，都会自动从列表中称除
         * 参数三：消息处理的回调方法
         * 注意：
         * basicConsume方法会在底层启动一个子线程，用于持续监听队列消息，所以不能关闭
         * */
        channel.basicConsume(queueName, false, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                // super.handleDelivery(consumerTag, envelope, properties, body);
                try {
                    String message = new String(body);
                    System.out.println("接收到了Direct => " + message);
                    // System.out.println(1 / 0);
                    // 手动确认消息，参数一：消息标号，参数二：批量确认
                    this.getChannel().basicAck(envelope.getDeliveryTag(), true);
                }catch (Exception e) {
                    // 将没处理完的消息重新放回到队列当中，等待下次处理
                    this.getChannel().basicRecover();
                }
            }
        });
    }
}
```


# 十、SpringBoot整合RabbitMQ


## 1. direct方式收发

### 1) direct发送

1. 创建模块，并添加起步依赖
![[Pasted image 20230714175006.png]]

2. 编写配置文件
```properties
# 简化控制台日志输出格式  
logging.pattern.console=%d{MM/dd-HH:mm:ss} ===> %msg%n  
  
# 配置MQ相关连接信息（单机版）  
spring.rabbitmq.host=192.168.10.128  
spring.rabbitmq.port=5672  
spring.rabbitmq.username=root  
spring.rabbitmq.password=root
```

3. 编写发送服务类`com.example.service.SendService.java`
```java
package com.example.service;

import org.springframework.amqp.core.AmqpTemplate;
// import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;

@Component // 把这个服务对象放到容器里
public class SendService {
    // 注入AMQP的模板工具类接口对象
    // 这个对象支持一些基本的发送和接收
    @Resource
    private AmqpTemplate amqpTemplate;

    // 注入RabbitTemplate模板工具类对象，这个类是AmqpTemplate接口的子类
    // 这个对象可以完成对RabbitMQ的基本操作，比如消息的发送、接收、确认模式等
    // @Resource
    // private RabbitTemplate rabbitTemplate;

    public void directSend(String message) {
        // 将String类型的参数转换成我想要的消息形式
        amqpTemplate.convertAndSend("directSpringBootExchange", "bodyKey", message);
    }
}
```

4. 编写配置类`com.example.conf.RabbitMQConf.java`
```java
package com.example.conf;

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.DirectExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class RabbitMQConf {
    @Bean
    public Queue directQueue() {
        // 声明队列，并定义到Spring的容器中
        return new Queue("directQueue");
    }

    @Bean
    public DirectExchange directExchange() {
        // 定义交换机对象，并定义到Spring容器中
        return new DirectExchange("directSpringBootExchange");
    }

    @Bean
    public Binding directBinding() {
        /*
         * 声明绑定规则
         * 参数一：目的地名称，可以是队列名，也可以是交换机名，根据参数二而定
         * 参数二：目的地类型，枚举类型，交换机或队列
         * 参数三：交换机名
         * 参数四：绑定时的routingKey
         * 参数五：绑定参数属性，通常为null
         * */
        return new Binding(
                "directQueue", // 队列名
                Binding.DestinationType.QUEUE, // 第一个参数（目的地）的类型
                "directSpringBootExchange", // 交换机名
                "bodyKey", // BindingKey
                null // 默认为空就行
        );
    }
}
```

5. 修改引导类
```java
package com.example;

import com.example.service.SendService;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import javax.annotation.Resource;

@SpringBootApplication
public class Mq09SpringBootApplication implements CommandLineRunner {

    @Resource
    private SendService sendService;

    public static void main(String[] args) {
        SpringApplication.run(Mq09SpringBootApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        sendService.directSend("SpringBoot direct send test message");
    }
}
```

6. 启动并测试发送，在管控台查看消息

### 2) direct接收

1. 创建模块，并添加起步依赖
2. 编写properties配置文件，参考发送
3. 编写配置，参考发送
4. 编写接收服务类
```java
package com.example.service;

// import org.springframework.amqp.core.AmqpTemplate;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

// import javax.annotation.Resource;

@Component
public class ReceiveService {
    // @Resource
    // private AmqpTemplate amqpTemplate;

    /*public void directReceive() {
        // 只能接收一条消息，不能持续监听队列
        String message = (String) amqpTemplate.receiveAndConvert("directQueue");
        System.out.println("正在消费 ==> " + message);
    }*/

    /*
    * 用于持续监听队列
    * 属性：
    * queues - 用于指定要监听的队列名，取值为String数组，这个属性只能监听队列，不能声明，所以必须存在
    * 注意：
    * 这个监听器，自带消费者确认模式，如果当前方法抛出异常，则将消息放回队列尾部，成功则将消息移除队列
    * 如果使用监听器监听消息，就不要再使用普通的接收方法
    * */
    @RabbitListener(queues = "directQueue")
    public void directMessageListener(String message) {
        System.out.println("正在消息 => " + message);
    }
}
```
5. 编写引导类 
```java
package com.example;

// import com.example.service.ReceiveService;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// import javax.annotation.Resource;

@SpringBootApplication
public class Mq10SpringBootReceiveApplication implements CommandLineRunner {
    // @Resource
    // private ReceiveService receiveService;

    public static void main(String[] args) {
        SpringApplication.run(Mq10SpringBootReceiveApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        // receiveService.directReceive();
    }
}
```
6. 运行接收和发送进行测试

## 2. fanout方式收发

### 1) fanout接收

`com.example.service.ReceiveService.java`
```java
package com.example.service;

// import org.springframework.amqp.core.AmqpTemplate;

import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

// import javax.annotation.Resource;

@Component
public class ReceiveService {
    // @Resource
    // private AmqpTemplate amqpTemplate;

    /*public void directReceive() {
        // 只能接收一条消息，不能持续监听队列
        String message = (String) amqpTemplate.receiveAndConvert("directQueue");
        System.out.println("正在消费 ==> " + message);
    }*/

    /*
     * 用于持续监听队列
     * 属性：
     * queues - 用于指定要监听的队列名，取值为String数组，这个属性只能监听队列，不能声明，所以必须存在
     * 注意：
     * 这个监听器，自带消费者确认模式，如果当前方法抛出异常，则将消息放回队列尾部，成功则将消息移除队列
     * 如果使用监听器监听消息，就不要再使用普通的接收方法
     * */
    /*@RabbitListener(queues = "directQueue")
    public void directMessageListener(String message) {
        System.out.println("正在消息 => " + message);
    }*/


    /*
    * 用于持续监听队列
    * 属性：
    * bindings 用于将队列和交换机进行绑定，取值为@QueueBinding数组
    *   属性：
    *   value 可用于声明使用这个队列，取值为 @Queue
    *       @Queue 用于声明一个队列
    *       属性：
    *           value / name  用于指定队列名称，不指定则创建随机队列
    *           durable  是否持久化，String类型 ”true“ 或 ”false“
    *           exclusive  是排外，String类型 ”true“ 或 ”false“
    *           autoDelete  是自动删除，String类型 ”true“ 或 ”false“
    *   exchange 用于声明并使用这个交换机，取值为 @Exchange
    *       @Exchange  用于声明一个交换机
    *       属性：
    *           value / name  用于指定交换机名称
    *           type  交换机类型，String类型，取值：”direct“ / ”fanout“ / ”topic“ / ”headers“ 默认值为 ”direct“
    *           durable
    *           autoDelete
    *   key  绑定是BindingKey 取值为 String 数组（可选）
    * */
    @RabbitListener(bindings = { @QueueBinding(value = @Queue, exchange = @Exchange(name = "fanoutSpringBootExchange", type = "fanout")) })
    public void fanoutMessageListener01(String message) {
        System.out.println("fanoutMessageListener01 --> " + message);
    }

    @RabbitListener(bindings = { @QueueBinding(value = @Queue, exchange = @Exchange(name = "fanoutSpringBootExchange", type = "fanout")) })
    public void fanoutMessageListener02(String message) {
        System.out.println("fanoutMessageListener02 --> " + message);
    }
}
```

### 2) fanout发送

- `com.example.service.SendService.java`
```java
// fanout 发送
public void fanoutSend(String message) {
	// 将String类型的参数转换成我想要的消息形式
	amqpTemplate.convertAndSend("fanoutSpringBootExchange", "", message);
}
```

- `com.example.conf.RabbitMqConf.java`
```java
@Bean
public FanoutExchange fanoutExchange() {
	// 定义交换机对象，并定义到Spring容器中
	return new FanoutExchange("fanoutSpringBootExchange");
}
```

- `引导类`
```java
@Override  
public void run(String... args) throws Exception {  
    // sendService.directSend("SpringBoot direct send test message");  
    sendService.fanoutSend("SpringBoot fanout send test message");  
}
```

## 3. topic方式收发

### 1) topic接收

`com.example.service.Receive.java`
```java
package com.example.service;

// import org.springframework.amqp.core.AmqpTemplate;

import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

// import javax.annotation.Resource;

@Component
public class ReceiveService {
    // @Resource
    // private AmqpTemplate amqpTemplate;

    /*public void directReceive() {
        // 只能接收一条消息，不能持续监听队列
        String message = (String) amqpTemplate.receiveAndConvert("directQueue");
        System.out.println("正在消费 ==> " + message);
    }*/

    /*
     * 用于持续监听队列
     * 属性：
     * queues - 用于指定要监听的队列名，取值为String数组，这个属性只能监听队列，不能声明，所以必须存在
     * 注意：
     * 这个监听器，自带消费者确认模式，如果当前方法抛出异常，则将消息放回队列尾部，成功则将消息移除队列
     * 如果使用监听器监听消息，就不要再使用普通的接收方法
     * */
    /*@RabbitListener(queues = "directQueue")
    public void directMessageListener(String message) {
        System.out.println("正在消息 => " + message);
    }*/


    /*
     * 用于持续监听队列
     * 属性：
     * bindings 用于将队列和交换机进行绑定，取值为@QueueBinding数组
     *   属性：
     *   value 可用于声明使用这个队列，取值为 @Queue
     *       @Queue 用于声明一个队列
     *       属性：
     *           value / name  用于指定队列名称，不指定则创建随机队列
     *           durable  是否持久化，String类型 ”true“ 或 ”false“
     *           exclusive  是排外，String类型 ”true“ 或 ”false“
     *           autoDelete  是自动删除，String类型 ”true“ 或 ”false“
     *   exchange 用于声明并使用这个交换机，取值为 @Exchange
     *       @Exchange  用于声明一个交换机
     *       属性：
     *           value / name  用于指定交换机名称
     *           type  交换机类型，String类型，取值：”direct“ / ”fanout“ / ”topic“ / ”headers“ 默认值为 ”direct“
     *           durable
     *           autoDelete
     *   key  绑定是BindingKey 取值为 String 数组（可选）
     * */
    /*@RabbitListener(bindings = { @QueueBinding(value = @Queue, exchange = @Exchange(name = "fanoutSpringBootExchange", type = "fanout")) })
    public void fanoutMessageListener01(String message) {
        System.out.println("fanoutMessageListener01 --> " + message);
    }

    @RabbitListener(bindings = { @QueueBinding(value = @Queue, exchange = @Exchange(name = "fanoutSpringBootExchange", type = "fanout")) })
    public void fanoutMessageListener02(String message) {
        System.out.println("fanoutMessageListener02 --> " + message);
    }*/


    @RabbitListener(bindings = {@QueueBinding(key = "it", value = @Queue(), exchange = @Exchange(name = "topicSpringBootExchange", type = "topic"))})
    public void topicMessageListener01(String message) {
        System.out.println("topicMessageList -01 --- BindingKey = it --> " + message);
    }

    @RabbitListener(bindings = {@QueueBinding(key = "it.*", value = @Queue(), exchange = @Exchange(name = "topicSpringBootExchange", type = "topic"))})
    public void topicMessageListener02(String message) {
        System.out.println("topicMessageList -02 --- BindingKey = it.* --> " + message);
    }

    @RabbitListener(bindings = {@QueueBinding(key = "it.#", value = @Queue(), exchange = @Exchange(name = "topicSpringBootExchange", type = "topic"))})
    public void topicMessageListener03(String message) {
        System.out.println("topicMessageList -03 --- BindingKey = it.# --> " + message);
    }
}
```

### 2) topic发送

- `SendService.java`
```java
// topic 订阅发送  
public void topicSend(String message, String routingKey) {  
    // 将String类型的参数转换成我想要的消息形式  
    amqpTemplate.convertAndSend("topicSpringBootExchange", routingKey, message);  
}
```

- `RabbitMQConf.java`
```java
@Bean
public TopicExchange topicExchange() {
	// 定义交换机对象，并定义到Spring容器中
	return new TopicExchange("topicSpringBootExchange");
}
```

- `引导类`
```java
package com.example;

import com.example.service.SendService;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import javax.annotation.Resource;

@SpringBootApplication
public class Mq09SpringBootApplication implements CommandLineRunner {

    @Resource
    private SendService sendService;

    public static void main(String[] args) {
        SpringApplication.run(Mq09SpringBootApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        // sendService.directSend("SpringBoot direct send test message");
        // sendService.fanoutSend("SpringBoot fanout send test message");
        sendService.topicSend("SpringBoot topic send test message", "it.maqf");
    }
}
```


# 十一、RabbitMQ集群

常见的RabbitMQ模式
- 普通模式（默认模式）
- 镜像模式（高可用模式）

## 1. 集群搭建过程 

1. 克隆两个已搭建RabbitMQ的CentOS7，分别命名为CentOS7-A和CentOS7-B
2. 修改主机名分别为A和B，配置文件`etc/hostname`
3. 清空之前的MQ使用痕迹，清空`/var/lib/rabbimq/mnesia/`下的所有内容
4. 修改RabbitMQ的节点名分别为`rabbit@A`和`rabbit@B`，配置文件`/etc/rabbitmq/rabbitmq-env.conf`
5. 保证两个系统中的`/var/lib/rabbitmq/.erlang.cookie`内容完全相同

6. 分别修改两个系统中的`/etc/hosts`文件设置主机名，之后重启系统（根据实际IP地址进行修改，配置完成后最好重启一下服务器）
```
# A hosts
127.0.0.1 A   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1       A   localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.10.129 A
192.168.10.130 B
```

```
# B hosts
127.0.0.1 B   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1       B   localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.10.129 A
192.168.10.130 B
```

7. 配置远程连接终端并连接，步骤参考之前安装
8. 分别启动两台RabbitMQ `rabbitmq-server start &`
9. 添加用户，并设权限，参考之前的用户管理和权限管理部分
10. 在B中添加A配置集群环境
```
rabbitmqctl stop_app
rabbitmqctl join_cluster rabbit@A
rabbitmqctl start_app
```

12. 查看并确认添加成功
```
rabbitmqctl cluster_status
```

13. 管控台查看配置是否生效
![[Pasted image 20230717141713.png]]

## 2. 项目中集群配置

```properties
# 简化控制台日志输出格式
logging.pattern.console=%d{MM/dd-HH:mm:ss} ==> %msg%n

# 配置MQ相关连接信息（集群版）
spring.rabbitmq.address=192.168.10.129:5672,192.168.10.130:5672
spring.rabbitmq.username=root
spring.rabbitmq.password=root
```

## 3. 实现高可用集群

![[Pasted image 20230717142142.png]]







