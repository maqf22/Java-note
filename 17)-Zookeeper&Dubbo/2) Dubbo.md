
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

@Component
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
dubbo.registry.address=zookeeper://localhost:2181
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
dubbo.registry.address=zookeeper://localhost:2181
```
- `com.example.controller.UserController.java`
```java
package com.example.controller;

import com.example.domain.User;
import com.example.service.UserService;
import org.apache.dubbo.config.annotation.DubboReference;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class UserController {
    // unicast=false 表示广播给所有订阅者，默认表示单播给自己地址的订阅者
    @DubboReference(version = "1.0", parameters = {"unicast", "false"})
    private UserService userService;

    @RequestMapping("/findAll")
    public List<User> findAll() {
        return userService.findAll();
    }

    @RequestMapping("/findById/{id}")
    public User findById(@PathVariable int id) {
        return userService.findById(id);
    }
}
```
- `DubboConsumerApplication.java`
```java
package com.example;  
  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
@SpringBootApplication  
public class DubboConsumerApplication {  
    public static void main(String[] args) {  
        SpringApplication.run(DubboConsumerApplication.class, args);  
    }  
}
```

> [!Tip]
> 如果不能同时运行两个可能是IDEA的问题，一个正常运行一个用Debug模式运行一般可以解决


# 四、Dubbo-admin

## 1.安装步骤

- 官网下载后解压 https://github.com/apache/dubbo-admin/tree/master
- 下载并安装Node.js
- 前后端分开部署

- 后端：SpringBoot打包部署启动，部署 期间需要修改项目中的配置文件
1.  `application.properties`
```properties
server.port=9527

# 修改为自己的注册中心配置，格式:协议://IP地址:端口号
admin.registry.address=zookeeper://localhost:2181
admin.config-center=zookeeper://localhost:2181
admin.metadata-report.address=zookeeper://localhost:2181
```
2. 打包启动
	1. 通过IDEA中的MAVEN工具打包
	2. 在命令执行`java -jar xxxx.jar`启动后端服务

- 前端：通过Node.js打包部署启动，部署期间需要修改项目中的配置文件
1. 进入`vue.config.js`配置文件并修改内容
```js
/*
 * Licensed to the Apache Software Foundation (ASF) under one or more
 * contributor license agreements.  See the NOTICE file distributed with
 * this work for additional information regarding copyright ownership.
 * The ASF licenses this file to You under the Apache License, Version 2.0
 * (the "License"); you may not use this file except in compliance with
 * the License.  You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

const path = require('path');

module.exports = {
  outputDir: "target/dist",
  lintOnSave: "warning",
  devServer: {
    port: 8088,
    historyApiFallback: {
      rewrites: [
        {from: /.*/, to: path.posix.join('/', 'index.html')},
      ],
    },
    publicPath: '/',
    proxy: {
      '/': {
        target: 'http://localhost:9527/',
        changeOrigin: true,
        pathRewrite: {
          '^/': '/'
        }
      }
    }
  },
  configureWebpack: {
    devtool: process.env.NODE_ENV === 'dev' ? 'source-map' : undefined,
    performance: {
      hints: false
    },
    optimization: {
      splitChunks: {
        cacheGroups: {
          reactBase: {
            name: 'braceBase',
            test: (module) => {
              return /brace/.test(module.context);
            },
            chunks: 'initial',
            priority: 10,
          },
          common: {
            name: 'vendor',
            chunks: 'initial',
            priority: 2,
            minChunks: 2,
          },
        }
      }
    }
  }
};
```
2. `npm install` & `npm run dev`




