
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


# 三、通过IDEA快速构建

![[Pasted image 20230625101921.png]]

> 注：https://start.spring.io如果经常创建失败，也可以使用国内镜像地https://start.springboot.io

![[Pasted image 20230625102154.png]]


# 四、SpringBoot配置

## 1. 配置文件的分类

- `application.properties`

```
server.port=8081
server.servlet.context-path=/api
```

- `application.yaml/application.yml`

```
server:
	port: 8082
	servlet:
		context-path: /api
```

- 配置优先级：.properties > .yml > .yaml

## 2. YAML语法

> 层级更明显，相比XML更加的简洁

```
server:
	port: 8081
	address: 127.0.0.1

teacher: Jack
student: Rose

# 行内模式
person: {name: Jack, age: 17}

# 数组常规写法
classid:
	- Jack
	- Rose
	- Ben

# 数组行内模式
classid : [Jack, Rose, Ben]

# 引号的使用
info01: 'Hello\nJack' # 不识别转义符
info02: "Hello\nRose" # 识别转义符

# 参数引用
hello: ${info01}
```

- 语法要求
	- 严格区分大小写
	- 数据前必须有空格，个数不限
	- 使用给缩进表示层级关系
	- 缩进是不允许`<tab>`，值允许使用空格（IDEA忽略）
	- 用`#`表示注释，单行注释
	- 支持行内写法（不常用，战略性了解）

## 3. 读取配置文件

> `application.yml`

```xml
name: Rose

person:
  name: Jack
  age: 17
  scores:
    - 100
    - 90
    - 80

lessons:
  - JavaSE
  - JavaEE
  - MySQL
  - Spring

info01: 'Hello\nSpringBoot'
info02: "Hello\nSpringBoot"
```

> `Person.java`

```java
package com.example.domain;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component // 放入容器
@ConfigurationProperties(prefix = "person") // 设置前缀
public class Person {
    private String name;
    private int age;
    private double[] scores;

    public Person() {
    }

    public Person(String name, int age, double[] scores) {
        this.name = name;
        this.age = age;
        this.scores = scores;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public double[] getScores() {
        return scores;
    }

    public void setScores(double[] scores) {
        this.scores = scores;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", scores=" + Arrays.toString(scores) +
                '}';
    }
}
```

> `SpringBoot03ReadConfConfApplicationTest.java`

```java
package com.example;

import com.example.domain.Person;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.core.env.Environment;

@SpringBootTest
class SpringBoot03ReadConfApplicationTests {
    @Value("${name}")
    private String name;
    @Autowired
    private Person person;
    @Value("${lessons[0]}")
    private String lessons01;
    @Value("${info01}")
    private String info01;
    @Value("${info02}")
    private String info02;

    @Autowired
    private Environment env;
    @Test
    void contextLoads() {
        System.out.println("name = " + name);

        System.out.println("person.name = " + person.getName());
        System.out.println("person.age = " + person.getAge());
        System.out.println("person.scores[0] = " + person.getScores()[0]);

        System.out.println("lessons[0] = " + lessons01);

        System.out.println("info01 = " + info01);
        System.out.println("info02 = " + info02);

        System.out.println("______");

        System.out.println("name = " + env.getProperty("name"));

        System.out.println("person.name = " + env.getProperty("person.name"));
        System.out.println("person.age = " + env.getProperty("person.age"));
        System.out.println("person.scores[0] = " + env.getProperty("person.scores[0]"));

        System.out.println("lesson[0] = " + env.getProperty("lessons[0]"));

        System.out.println("info01 = " + env.getProperty("info01"));
        System.out.println("info02 = " + env.getProperty("info02"));
    }
}
```

## 4. PROFILE

> profile可以动态切换开发、测试、生产环境中的配置

- profile配置方式

1. 多profile文件

```properties
# application-dev.properties
server.port=8081
```

```properties
# application-pro.properties
server.port=8082
```

```properties
# application-test.properties
server.port=8083
```

```properties
# application.properties（主配置文件）
spring.profiles.avtive=dev
```

2. yml多模块方式

```yml
---
spring:
  profiles:
    active: test

---
server:
  port: 8081

spring:
  config:
    activate:
      on-profile: dev

---
server:
  port: 8082

spring:
  config:
    activate:
      on-profile: pro

---
server:
  port: 8083

spring:
  config:
    activate:
      on-profile: test
```

## 5. 加载顺序

### 1) 内部配置加载顺序

> 按照优先级排列

1. 当前项目`/config`目录下：`file:./config/`（不参与打包）
2. 当前项目`/`根目录：`file:./`（不参与打包）
3. `classpath`的`/conf`目录：`calsspath:/config`
4. `classpath`的`/`根目录：`classpath:/`

![[Pasted image 20230625160627.png]]

> 注意：所有配置文件都会被加载，只有在有冲突的时候，才会使用优先级的关系覆盖重复的配置

### 2) 外部配置加载顺序

> 外部配置会优先生效

1. 命令行指定配置 - `java -jar xxxx.jar --server.prot 8081`
2. 命令行指定的配置文件位置 - `java -jar xxxx.jar --spring.config.location=xxxx.application.properties`
3. 命令行默认读取配置文件
	- jar包所在目录下`application-xx.properties`
	- jar包内的`application-xx.properties`
	- jar包所在目录里`application.properties`
	- jar包内的`application.properties`


# 五、SpringBoot整合JSP

1. 创建SpringBoot工程
2. 导入依赖（pom.xml）
```xml
<!-- 引入Spring Boot内嵌的Tomcat对JSP的解析包，不加解析不了jsp页面 -->
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-jasper</artifactId>
</dependency>
<!-- servlet依赖的jar包start,可选 -->
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
</dependency>
<!-- jsp依赖jar包start，可选 -->
<dependency>
	<groupId>javax.servlet.jsp</groupId>
	<artifactId>javax.servlet.jsp-api</artifactId>
	<version>2.3.1</version>
</dependency>
<!-- jstl标签依赖的jar包start，可选 -->
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>jstl</artifactId>
</dependency>
```
3. 新建`/src/main/webapp`目录，并添加`index.jsp`文件
```jsp
<%@ page language="java" contentType="text/html; charset-UTF=8" pageEncoding="UTF-8" %>
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="x-ua-compatible" content="ie=edge">
    <title>Test page</title>
</head>
<body>
<h1>SpringBoot整合JSP测试页面</h1>
<hr/>
<p>${info}</p>
</body>
</html>
```
4. 创建controller业务层控制器
```java
package com.example.controller;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.RequestMapping;  
  
@Controller  
public class Server {  
    @RequestMapping("/")  
    public String test(Model model) {  
        System.out.println("test() is running~");  
        model.addAttribute("info", "Hello JSP");  
        return "index.jsp";  
    }  
}
```
5. `application.properties`配置文件中添加配置
```properties
# 相当于/src/main/webapp目录
spring.mvc.view.prefix=/
spring.mvc.view.suffix=.jsp
```
6. `pom.xml`的`<build>`标签中添加字标签配置
```xml
<resources>
	<resource>
		<!-- 源文件位置 -->
		<directory>src/main/webapp</directory>
		<!-- 编译到META-INF/resources，该目录不能随便写 -->
		<targetPath>META-INF/resources</targetPath>
		<includes>
			<!-- 要把哪些文件编译过去，**表示webapp目录及子目录，*.*表示所有 -->
			<include>**/*.*</include>
		</includes>
	</resource>
</resources>
```
7. 测试
`http://localhost:8080/`


# 六、SpringBoot整合Junit

> 通过IDEA创建的SpringBoot项目默认整合了Junit，使用的时候需要注意测试类的包路径，如果测试类的包路径与引导类不一致，需要注解参数中添加指定类的字节码文件参数`classes = SpringBoot06JspApplication.class`

```java
package com.example;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest(classes = SpringBoot06JspApplication.class)
class SpringBoot06JspApplicationTests {
    @Test
    void contextLoads() {
    }
}
```


# 七、SpringBoot整合MyBatis

## 1. MyBatis整合步骤

