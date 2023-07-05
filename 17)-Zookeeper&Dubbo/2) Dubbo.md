
# 一、概述

## 1. 分布式系统

> 分布式系统就是若干个独立的计算机的集合，这些计算机对于用户而言就像一个单个的系统

## 2. 发展演变

![[Pasted image 20230705115338.png]]

## 3. RPC简介


> Remote Procedure Call 是指远程过程调用，帮我们解决A服务器如何去调用B/C/D中相关的方法

## 4. Dubbo架构

![[Pasted image 20230705122207.png]]

- Provider：暴露服务的生产者
- Container：服务运行容器
- Consumer：调用远程服务的服务消费者
- Registry：服务注册与发现（注册中心）
- Monitor：统计服务的调用次数和调用时间监控中心
- init：初始化
- async：异步
- sync：同步


# 二、注册中心

> 使用Zookeeper作为注册中心


# 三、快速上手

## SpringBoot整合Dubbo步骤

1. 创建父类maven项目SpringBoot-Dubbo
2. 创建三个字maven项目
	1. Dubbo-API - 公共接口
	2. Dubbo-Provider - 服务提供者
	3. Dubbo-Consumer - 服务消费者
3. SpringBoot-Dubbo父类项目中`pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- SpringBoot父类依赖 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.13</version>
    </parent>

    <groupId>org.example</groupId>
    <artifactId>SpringBoot-Dubbo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!-- 统一版本号 -->
        <dubbo.version>3.0.5</dubbo.version>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <!-- Dubbo依赖 -->
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>${dubbo.version}</version>
        </dependency>
        <!-- zookeeper依赖，用于注册中心 -->
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-dependencies-zookeeper</artifactId>
            <version>${dubbo.version}</version>
            <type>pom</type>
        </dependency>
    </dependencies>

</project>
```

4. 完善Dubbo-API公共接口子项目
- `pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.example</groupId>
        <artifactId>SpringBoot-Dubbo</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>Dubbo-API</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
    
    <dependencies>
	    <!-- lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>

</project>
```
- `com.example.domain.User.java`
```java
package com.example.domain;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.ToString;

@Data
@AllArgsConstructor
@ToString
public class User {
    private Integer id;
    private String username;
    private String password;
}
```
- `com.example.service.UserService.java`
```java
package com.example.service;

import com.example.domain.User;

import java.util.List;

public interface UserService {
    List<User> findAll();
    User findById(int id);
}
```

5. 完善Dubbo-Provider服务提供者子项目
- `pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.example</groupId>
        <artifactId>SpringBoot-Dubbo</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>Dubbo-Provider</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- 父类项目依赖 -->
        <dependency>
            <groupId>org.example</groupId>
            <artifactId>Dubbo-API</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!-- web依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
```
- `application.properties`
```properties
# 服务端口号设置
server.port=8082

# 应用名称
dubbo.application.name=dubbo-provider
# 注册中心
dubbo.registry.address=zookeeper://127.0.0.1:2181
# 指定协议和端口号
dubbo.protocol.name=dubbo
dubbo.protocol.port=29527
# 扫描指定的包
dubbo.scan.base-packages=com.example.service.impl
```
- `com.example.service.impl.UserServiceImpl.java`
```java
package com.example.service.impl;

import com.example.domain.User;
import com.example.service.UserService;
import org.apache.dubbo.config.annotation.DubboService;

import java.util.ArrayList;
import java.util.List;

@DubboService(version = "1.0") // 版本号设置可选
public class UserServiceImpl implements UserService {
    @Override
    public List<User> findAll() {
        // 模拟获取数据
        ArrayList<User> users = new ArrayList<>();
        users.add(new User(0, "Jack", "123"));
        users.add(new User(1, "Rose", "456"));
        return users;
    }

    @Override
    public User findById(int id) {
        return new User(id, "Lily", "789");
    }
}
```
- `com.example.DubboProviderApplication.java`
```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DubboProviderApplication {
    public static void main(String[] args) {
        // 引导类主方法
        SpringApplication.run(DubboProviderApplication.class, args);
    }
}
```

6. 完善Dubbo-Consumer消费者子项目
- `pom.xml`（与服务提供端相同）
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.example</groupId>
        <artifactId>SpringBoot-Dubbo</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>Dubbo-Consumer</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.example</groupId>
            <artifactId>Dubbo-API</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
</project>
```
- `application.properties`
```properties
# 服务端口号
server.port=8081

# 应用名称
dubbo.application.name=dubbo-consumer
# 注册中心
dubbo.registry.address=zookeeper://127.0.0.1:2181
```





