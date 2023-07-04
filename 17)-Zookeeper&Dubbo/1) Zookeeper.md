
# 一、简介、

ZooKeeper 是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，是Hadoop和Hbase的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。


# 二、安装

> 由于`zookeeper`采用`Java`编写的，所以我们需要`Java`环境才能运行 此处我们采用`open-jdk-8` 直接一行命令即可安装 `sudo apt-get install openjdk-8-jre`

## 1. 下载Zookeeper

打开zookeeper官网，可以找到对应的下载地址：`https://dlcdn.apache.org/zookeeper/zookeeper-3.5.10/`

使用wget下载：
`wget https://dlcdn.apache.org/zookeeper/zookeeper-3.5.10/`

## 2. 解压zookeeper

`tar -xvf zookeeper-3.5.10.tar.gz`

## 3. 移动解压的文件夹到安装目录

安装目录可以为任意，此处我们安装在`/usr/local/zookeeper/`下，因此，将刚才下载解压完成的文件夹移动到`/usr/local/zookeeper/`

`mv zookeeper-3.5.10/usr/local/zookeeper/zookeeper-3.5.10`

## 4. 为zookeeper创建软链接

为了方便以后`zookeeper`的版本更新，我们安装`zookeeper`的时候可以在同级目录下创建一个不带版本号的软链接，然后将其指向带zookeeper的真正目录

即创建一个名为`/usr/local/zookeeper/apache-zookeeper`的软链接，指向`/usr/local/zookeeper/zookeeper-3.5.10，以后更新版本的话，只需要修改软链接的指向，而我们配置的环境变量都不需要做任何更改

`ln -s /usr/local/zookeeper/zookeeper-3.5.10 /usr/local/zookeeper/apache-zookeeper`

## 5. 修改PATH

将zookeeper添加到环境变量

`vim /etc/profile`

新增下面两行

```
# 此处使用刚刚创建的软链接
export ZK_HOME=/usr/local/zookeeper/apache-zookeeper
export PATH=${ZK_HOME}/bin:${PATH}
```

让刚才修改的path生效

`source /etc/profile`

## 6. 修改zoo.cfg文件

zookeeper启动时候，会读取`conf`文件夹下的`zoo.cfg`作为配置文件，因此，我们可以将源码提供的示例配置文件复制一个，做一些修改

```shell
# 进入配置文件目录
$ cd /usr/local/zookeeper/apache-zookeeper/conf

# 复制示例配置文件
cp zoo_sample.cfg zoo.cfg

# 修改 zoo.cfg 配置文件
vim zoo.cfg
```

修改数据存放位置 `dataDir=/usr/local/zookeeper/data`

```init
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.


# 只需要修改此处为zookeeper数据存放位置
dataDir=/usr/local/zookeeper/data
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1             
```

> [!Tip]
> 3.5版本以后会自动把8080端口占用
> 如果要使用服务器直接换一个端口号即可，配置文件conf中zoo.cfg添加`admin.serverPort=10086`
> 如果不使用服务器，直接将其禁用即可，也就是在配置文件zoo.cfg中添加命令`admin.enableServer=false`

## 7. 开始运行

进入bin目录，执行运行命令(由于刚才设置了`zookeeper`到环境变量`PATH`里面，所以，我们其实可以在任何目录下执行下面的命令)

```shell
# 进入bin目录
$ cd /usr/local/zookeeper/apache-zookeeper/bin

# 执行启动命令
$ ./zkServer.sh start

ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/apache-zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

# 查看状态
$ ./zkServer.sh status 
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/apache-zookeeper/bin/../conf/zoo.cfg
Mode: standalone
```

命令行输出运行成功，代表运行成功。


# 三、Zookeeper操作指令

## 1. 数据模型

- Zookeeper是一个树形目录服务，其数据模型和Unix的文件系统目录树类似，拥有一个层次化结构

![[Pasted image 20230704103740.png]]

- 每一个节点都被称为：ZNode，每个节点上都会保存自己的数据和节点信息
- 节点可以拥有子节点，同时也允许少量（1MB）数据存储在该节点之下
- 节点可以分为四大类：
	- 持久化节点
	- 持久化顺序节点`-s`
	- 临时节点`-e`
	- 临时顺序节点`-es`

## 2. 客户端常用操作指令

命令 | 说明
:- | :-
`./zkCli.sh -server localhost:2181` | 通过客户端连接服务器
`quit` | 退出客户端
`create <节点名> [值]` | 创建节点
`get <节点名>` | 获取节点中的值
`set <节占名> [值]` | 修改节点中的值
`delete <空节点名>` | 删除空节点
`deleteall <节点名>` | 删除任意节点（非空也可以）
`ls <节点名>` | 查看节点中包含的所有子节点
`help` | 帮助命令


# 四、Zookeeper JavaAPI

## 1. Curator API

> Curator是Apache Zookeeper的Java客户端API

## 2. Curator的常用操作

### 1) 建立连接

- `pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>ZookeeperTest</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>5.2.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>5.2.0</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.33</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.33</version>
        </dependency>
    </dependencies>

</project>
```

- `log4j.properties`
```properties
log4j.rootLogger=off,stdout  
  
log4j.appender.stdout=org.apache.log4j.ConsoleAppender  
log4j.appender.stdout.Target=system.out  
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
```

- `CuratorTest.java`
```java
package com.example.test;

import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.retry.ExponentialBackoffRetry;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

public class CuratorTest {
    private CuratorFramework client;

    @Before
    // 连接Zookeeper
    public void curatorConnect() {
        // 定义重试的策略
        ExponentialBackoffRetry retry = new ExponentialBackoffRetry(5000, 10);
        // 获取连接对象
        client = CuratorFrameworkFactory.builder()
                .connectString("127.0.0.1:2181")  // 连接IP:PORT
                .retryPolicy(retry)  // 重试策略
                .namespace("maqf")  // 当前连接的命名空间
                .build();  // 构建对象
        // 启动客户端并建立连接
        client.start();
    }

    @After
    // 断开
    public void curatorClose() {
        client.close();
    }
}
```

### 2) 修改节点

```java
// ==========

    @Test
    // 创建一个空节点
    public void createTest01() throws Exception {
        String path = client.create().forPath("/lesson");
        // 创建的空节点值不是真的为空，则是本机的ip地址
        System.out.println(path);
    }

    @Test
    // 创建一个带值的节点
    public void createTest02() throws Exception {
        String path = client.create().forPath("/lesson", "Zookeeper".getBytes());
        System.out.println(path);
    }

    @Test
    // 创建一个临时的节点
    public void createTest03() throws Exception {
        String path = client.create().withMode(CreateMode.EPHEMERAL).forPath("/lesson", "Zookeeper".getBytes());
        System.out.println(path);
    }

    @Test
    // 一次性创建多个（多层级）节点
    public void createTest04() throws Exception {
        String path = client.create().creatingParentsIfNeeded().forPath("/lesson/java/thread", "Zookeeper".getBytes());
        System.out.println(path);
    }
```

### 3) 查询节点

```java
// ==========

    @Test
    // 查询节点中的值
    public void getTest01() throws Exception {
        byte[] info = client.getData().forPath("/lesson");
        System.out.println(new String(info));
    }

    @Test
    // 查询子节点
    public void getTest02() throws Exception {
        List<String> pathList = client.getChildren().forPath("/");
        for (String s : pathList) {
            System.out.println(s);
        }
    }

    @Test
    // 查询节点的状态
    public void getTest03() throws Exception {
        Stat stat = new Stat();
        byte[] path = client.getData().storingStatIn(stat).forPath("/");
        System.out.println(new String(path));
        System.out.println(stat);
        System.out.println(stat.getVersion());
        System.out.println(stat.getNumChildren());
    }
```

属性 | 说明 
:- | :-
cZxid | 创建节点时的事务ID
ctime | 创建节点时的时间
mZxid | 最后修改节点时的事务ID
pZxid | 表示该节点的子节点列表最后一次修改的事务ID，<br>添加子节点或删除子节点就会影响子节点列表，<br>但是修改子节点的数据内容不影响ID，<br>（注意，只有子节点列表变更才会变更pZxid，子节点内容变更不会影响pZxid）
cversion | 子节点版本号，数据每次修改版本号加1
dataVersion | 数据版本号，数据每次修改该版本号加1
aclVersion | 权限版本号，权限每次修改该版本号加1
ephemeralOwner | 创建该临时节点的会话的sessionID。（如果该节点是持久节点，那么这个属性值为0）
dataLength | 该节点的数据长度
numChildren | 直接子节点的数量

### 4) 修改节点

```java
// ==========
    @Test
    // 修改节点
    public void setTest01() throws Exception {
        Stat stat = client.setData().forPath("/lesson", "okay".getBytes());
        System.out.println(stat);
        byte[] info = client.getData().forPath("/lesson");
        System.out.println(new String(info));
    }

    @Test
    // 根据版本号进行修改
    public void setTest02() throws Exception {
        Stat stat = new Stat();
        client.getData().storingStatIn(stat).forPath("/lesson");

        client.setData().withVersion(stat.getVersion()).forPath("/lesson", "zookeeperApi".getBytes());
    }
```

### 5) 删除节点

```java
// ==========
    @Test
    // 删除单个节点
    public void deleteTest01() throws Exception {
        client.delete().forPath("/lesson");
    }

    @Test
    // 删除带有子节点的节点
    public void deleteTest02() throws Exception {
        client.delete().deletingChildrenIfNeeded().forPath("/lesson");
    }

    @Test
    // 一定程度上可以确保删除成功的一种删除形式
    public void deleteTest03() throws Exception {
        client.delete().guaranteed().forPath("/lesson");
    }

    @Test
    // 删除动作之后调用一个回调函数
    public void deleteTest04() throws Exception {
        client.delete().guaranteed().inBackground(new BackgroundCallback() {
            @Override
            public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
                System.out.println("after delete...");
                System.out.println(curatorEvent.getPath() + " has be delete...");
            }
        }).forPath("/lesson");
    }
```

### 6) 事件监听

`Cache`可以对节点进行监听，如果被监听的节点发生了变化，将会通知所有这个节点的所有其他订阅者
`zookeeper`的三种监听器
- `NodeCache`：监听某一个指定的节点
- `PathchildrenCache`：监听一个ZNode的子节点
- `TreeCache`：监听整个树形结构的所有子节点
- `CuratorCache`：新的监听方式（Curator 5.0之后的新方法）

```java
// ==========
    @Test
    public void listenTest() throws IOException {
        // 创建监听对象
        CuratorCache cache = CuratorCache.build(client, "/");
        // 设置监听
        CuratorCacheListener listener = CuratorCacheListener.builder()
                .forCreates(node -> System.out.println("创建了一个节点：" + node))
                .forChanges((oldNode, node) -> System.out.println("节点更新了，原：" + oldNode + " 新：" + node))
                .forDeletes(oldNode -> System.out.println("节点被删除了：" + oldNode))
                .forInitialized(() -> System.out.println("被始化..."))
                .build();
        // 注册监听
        cache.listenable().addListener(listener);
        // 启动监听
        cache.start();
        // 阻塞
        System.in.read();
    }
```

### 7) 分布式锁

跨设备的进程之间的数据同步锁
- 分布式锁的实现方式
	- 基于缓存来实（性能高，不可靠）
	- 通过数据库实现（效率低，不推荐）
	- Zookeeper实现（相对平衡）
- Zookeeper分布式锁原理
	- 客户端获取锁时，在lock节点下创建**监时的顺序**节点
	- 然后获取lock下面的所有子节点，客户端获取到所有的子节点之后，如果发现自己创建的子节点号最小，就说明当前客户端获取到了锁，使用完之后，释放锁就是删除之个节点
	- 如果发现自己创建的节点并非lock所有子节点中最小的，说明自己还没获取到锁，此时客户端需要找到比自己小的那个节点，同时对其注册监听器，监听删除事件。如果发现删除了，说明上一个锁已经释放了
	- 当发现了上一个锁释放了之后，再次判断自己是不是最小的，如果是我就是获取到了，如果不是重复以上步骤

- Curator分布式锁相关API





