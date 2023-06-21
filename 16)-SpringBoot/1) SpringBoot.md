
# 一、概述

> 简化Spring开发中的各种配置，用于项目快速构建。

Spring vs Spring Boot

Spring | Spring Boot
:- | :-
配置繁琐 | 自动配置，用来解决Spring配置繁琐的问题
依赖繁琐 | 起步依赖，用来解决Spring依赖繁琐的问题
自给自足 | 其他辅助功能，如：嵌入式服务器、安全、指标、健康检测、外部配置等


# 二、快速上手

> 快速搭建一个SpringBoot项目

1. 创建Maven项目

2. 导入SpringBoot起步依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>SpringBoot01-start</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!-- SpringBoot 继承父类工程的坐标 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.6.6</version>
    </parent>
    <dependencies>
        <!-- web 开发起步依赖配置 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
```

3. 定义Controller类
```java
package com.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@ResponseBody
public class HelloSpringBoot {
    @RequestMapping("/hello")
    public String hello() {
        return "Hello SpringBoot~";
    }
}
```

4. 编写引导类
```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// 引导类，SpringBoot中启动必须的类
@SpringBootApplication
public class MySpringBootApplication {
    public static void main(String[] args) {
        SpringApplication.run(MySpringBootApplication.class, args);
    }
}
```

5. 测试`localhost:8080/hello`


