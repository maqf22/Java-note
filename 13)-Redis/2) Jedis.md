
> 用于让Redis集合程序源码的工具

# 一、快速上手

> 步骤

1. 新建maven模块

2. 导入坐标（pom.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>JedisDemo01</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

3. 测试

```java
package com.example;

import org.junit.Test;
import redis.clients.jedis.Jedis;

public class JedisTest {
    @Test
    public void test01() {
        // 连接Redis
        Jedis jedis = new Jedis("127.0.0.1", 6379);

        jedis.set("infoFromJava", "Jedis");
        String infoFromJava = jedis.get("infoFromJava");
        System.out.printf("infoFromJava: %s\n", infoFromJava);

        // 关闭连接
        jedis.close();
    }

	@Test  
	public void test02() {  
	    Jedis jedis = new Jedis("127.0.0.1", 6379);  
	  
	    Long rpush = jedis.rpush("names", "Jack", "Rose", "Lily");  
	    System.out.println("rpush=" + rpush);  
	    List<String> names = jedis.lrange("names", 0, -1);  
	    for (String name : names) {  
	        System.out.println(name);  
	    }  
	  
	    jedis.close();  
	}
}
```

## 2. 常规操作

> 结合之前的Redis学习，尝试通过jedis操作之前学习过的所有Redis命令

## 3. 结合案例

1. 设置Redis配置文件`redis.properties`

```
redis.host=127.0.0.1
redis.port=6379
redis.maxTotal=30
redis.maxIdle=10
```

2. 创建Redis工具类`JedisUtils`，用于获取链接

```java
package com.example;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

import java.util.ResourceBundle;

public class JedisUtils {
    private static String host = null; // 连接IP地址
    private static String port = null; // 端口号
    private static String maxTotal = null; // 最大连接数
    private static String maxIdle = null; // 最大活跃数

    private static JedisPool jedisPool = null;

    static {
        // 获取配置文件对象
        ResourceBundle resourceBundle = ResourceBundle.getBundle("redis");

        // 通过配置文件中的键获取值
        host = resourceBundle.getString("redis.host");
        port = resourceBundle.getString("redis.port");
        maxTotal = resourceBundle.getString("redis.maxTotal");
        maxIdle = resourceBundle.getString("redis.maxIdle");

        // 创建Redis配置对象
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();

        // 设置详细配置参数
        jedisPoolConfig.setMaxTotal(Integer.parseInt(maxTotal));
        jedisPoolConfig.setMaxIdle(Integer.parseInt(maxIdle));
        // 其他配置...

        // 创建连接
        jedisPool = new JedisPool(jedisPoolConfig, host, Integer.parseInt(port));
    }

    public static Jedis getJedis() {
        return jedisPool.getResource();
    }
}
```

3. 创建业务层`Service.java`

```java
package com.example;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.exceptions.JedisDataException;

public class Service {
    private final String id; // 用户级别
    private final int num; // 操作次数
    public Service(String id, int num) {
        this.id = id;
        this.num = num;
    }
    // 权限控制单元
    public void service() {
        Jedis jedis = JedisUtils.getJedis();
        String value = jedis.get("USERID:" + id);
        // 判断这个Key是否存在
        try {
            if (value == null) { // 不存在，则新建这个Key，设置时效
                jedis.setex("USERID:" + id, 10, Long.MAX_VALUE - num + "");
            } else {
                // 如果存在，先自增，再执行下载
                Long val = jedis.incr("USERID:" + id);
                download(id, num - (Long.MAX_VALUE - val));
            }
        } catch (JedisDataException e) {
            System.out.println(Thread.currentThread().getName() + " >>> 下载已经到达次数上限");
        } finally {
            jedis.close();
        }
    }

    // 业务操作
    public void download(String id, Long val) {
        System.out.printf("用户：%s 下载第%d次\n", id, val);
    }
}
```

4. 创建多线程（模拟多用户操作）`MyRun.java`

```java
package com.example;

public class MyRun implements Runnable {
    private Service sc = null; // 业务层对象

    public MyRun(String id, int num) {
        sc = new Service(id, num);
    }

    @Override
    public void run() {
        while (true) {
            sc.service();
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

5. 测试

```java
package com.example;

public class TestMain {
    public static void main(String[] args) {
        Thread visitor = new Thread(new MyRun("visitor", 5));
        Thread normal = new Thread(new MyRun("normal", 10));
        Thread vip = new Thread(new MyRun("vip", 50));

        visitor.start();
        normal.start();
        vip.start();
    }
}
```

> 注意Redis服务要事先启动

