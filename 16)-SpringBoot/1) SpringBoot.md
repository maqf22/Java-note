
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

1. 创建SpringBoot工程

2. 引入MyBatis起步依赖并添加MySQL驱动
![[Pasted image 20230626095015.png]]

3. 编写DataSource与MyBatis相关配置信息（resources > application.yml）
```xml
# DataSource Configure  
spring:  
  datasource:  
    #url: jdbc:mysql:///spring_data?useUnicode=true&characterEncoding=utf8&useSSL=false  
    url: jdbc:mysql://127.0.0.1:3306/spring_data?useUnicode=true&characterEncoding=utf8&useSSL=false  
    driver-class-name: com.mysql.cj.jdbc.Driver  
    username: root  
    password: root

mybatis:  
  # config-location：用于指定MyBatis核心配置文件  
  mapper-locations: classpath:mapper/*Mapper.xml  
  type=aliases-package: com.example.domain
```

4. 创建表、定义实体类
- `Stu`数据表备份
```sql
set foreign_key_checks=0;

drop table if exists `stu`;
create table `stu` (
	`id` int(10) unsigned not null auto_increment,
	`name` varchar(32) not null,
	`sex` enum('0', '1', '2') default '0',
	`age` tinyint(3) unsigned default '18',
	`score` float(6,2) default '0.00',
	`tel` varchar(32) not null,
	`classid` varchar(16) default 'SpringBoot',
	primary key(`id`),
	unique key `tel` (`tel`)
)engine=MyISAM auto_increment=1 default charset=utf8;

insert into `stu` values
('1', 'Andy', '1', '19', '100.00', '12345678901', 'JavaSE'),
...;
```
- `Stu.java`实体类
```java
package com.example.domain;

public class Stu {
    private int id;
    private String name;
    private String sex;
    private Integer age;
    private Float score;
    private String tel;

    public Stu() {
    }

    public Stu(String name, String sex, Integer age, Float score, String tel) {
        this.name = name;
        this.sex = sex;
        this.age = age;
        this.score = score;
        this.tel = tel;
    }

    public Stu(int id, String name, String sex, Integer age, Float score, String tel) {
        this.id = id;
        this.name = name;
        this.sex = sex;
        this.age = age;
        this.score = score;
        this.tel = tel;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Float getScore() {
        return score;
    }

    public void setScore(Float score) {
        this.score = score;
    }

    public String getTel() {
        return tel;
    }

    public void setTel(String tel) {
        this.tel = tel;
    }

    @Override
    public String toString() {
        return "Stu{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", sex='" + sex + '\'' +
                ", age=" + age +
                ", score=" + score +
                ", tel='" + tel + '\'' +
                '}';
    }
}
```

5. 编写dao与mapper文件/纯注解开发
- 注解
- `StuMapper.java`
```java
package com.example.mapper;  
  
import com.example.domain.Stu;  
import org.apache.ibatis.annotations.Mapper;  
import org.apache.ibatis.annotations.Select;  
  
import java.util.List;  
  
@Mapper  
public interface StuMapper {  
    @Select("select * from `stu`")  
    List<Stu> findAll();  
}
```

- xml
- `resources/mapper/StuMapper.xml`
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.StuXmlMapper">
	<select id="findAll" resultType="Stu">
		select * from stu
	</select>
</mapper>
```
- `StuXmlMapper.java`
```java
package com.example.mapper;  
  
import com.example.domain.Stu;  
import org.apache.ibatis.annotations.Mapper;  
import org.springframework.stereotype.Repository; 
  
import java.util.List;  
  
@Mapper
@Repository
public interface StuXmlMapper {   
    List<Stu> findAll();  
}
```

6. `pom.xml`中添加
```xml
<resources>
	<resource>
		<directory>src/main/java</directory>
		<includes>
			<include>**/*.xml</include>
		</includes>
		<filtering>false</filtering>
	</resource>
</resources>
```

8. 测试
```java
package com.example;

import com.example.domain.Stu;
import com.example.mapper.StuMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

@SpringBootTest
class SpringBoot07MyBatisApplicationTests {
    @Autowired
    private StuMapper stuMapper;
    @Test
    void findAllTest() {
        List<Stu> stuList = stuMapper.findAll();
        for (Stu stu : stuList) {
            System.out.println(stu);
        }
    }
}
```

## 2. MyBatis结合逆向工程

1. 创建SpringBoot模块

2. 选择MyBatis起步依赖，MySQL驱动

3. 编写application.properties配置文件
```properties
# 设置端口号
server.port=8081

# 数据库连接设置
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/spring_data?useUnicode=true&characterEncoding=utf8&useSSL=false
spring.datasource.username=root
spring.datasource.password=root
```

4. `pom.xml`中添加坐标支持
```xml
<!-- mybatis逆向工程依赖2个 -->
<dependency>
	<groupId>org.mybatis.generator</groupId>
	<artifactId>mybatis-generator-core</artifactId>
	<version>1.3.7</version>
</dependency>
<dependency>
	<groupId>org.mybatis.generator</groupId>
	<artifactId>mybatis-generator-maven-plugin</artifactId>
	<version>1.3.7</version>
</dependency>
```

5. `pom.xml`中添加插件支持
```xml
<plugin>
	<groupId>org.mybatis.generator</groupId>
	<artifactId>mybatis-generator-maven-plugin</artifactId>
	<version>1.3.6<version>
	<configuration>
		<!-- 配置文件的位置 -->
		<configurationFile>GeneratorConfig.xml</configurationFile>
		<verbose>true</verbose>
		<overwrite>true</overwrite>
	</configuration>
</plugin>
```

6. 添加反向工程配置文件`GeneratorConfig.xml`
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE generatorConfiguration PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN" "http://mybatis.org/dtd/mybatis-generator-config_1._0.dtd">

<generatorConfiguration>

	<!-- 指定连接数据库的JDBC驱动包所在位置，指定到你本机的完整路径 -->
	<classPathEntry location="D:\Program Files\Java\repository\mysql\mysql-connector-java\5.1.47\mysql-connector-java-5.1.47.jar"/>

	<!-- 配置table表信息内容体，targetRuntime指定采用MyBatis3的版本 -->
	<context id="tables" targetRuntime="MyBatis3">

		<!-- 抑制生成注释，由于生成的注释都是英文的，可以不让它生成 -->
		<commentGenerator>
			<property name="suppressAllComments" value="true" />
		</commentGenerator>
		
		<!-- 配置数据库连接信息 -->
		<jdbcConnection driverClass="com.mysql.jdbc.Driver"
						connectionURL="jdbc:mysql://127.0.0.1:3306/spring_data?useSSL=false"
						userId="root"
						password="root">
		</jdbcConnection>

		<!-- 生成model类，targetPackage指定model类的包名，targetProject指定生成的model放在eclipse的哪个工程下面 -->
		<javaModelGenerator targetPackage="com.example.model" targetProject="src/main/java">
			<property name="enableSubPackages" value="false" />
			<property name="trimStrings" value="false" />
		</javaModelGenerator>

		<!-- 生成MyBatis的Mapper.xml文件，targetPackage指定mapper.xml文件的包名，targetProject指定生成的mapper.xml放在eclipse的哪个工程下面 -->
		<sqlMapGenerator targetPackage="com.example.mapper" targetProject="src/main/java">
			<property name="enableSubPackages" value="false" />
		</sqlMapGenerator>

		<!-- 生成MyBatis的Mapper接口类文件，targetPackage指定Mapper接口类的包名，targetProject指定生成的Mapper接口放在eclipse在哪个工程下面 -->
		<javaClientGenerator type="XMLMAPPER" targetPackage="com.example.mapper" targetProject="src/main/java">
			<property name="enableSubPackages" value="false" />
		</javaClientGenerator>

		<!-- 数据库表名及对应的Java模型类名 -->
		<table tableName="stu"
				domainObjectName="Stu"
				enableCountByExample="false"
				enableUpdateByExample="false"
				enableDeleteByExample="false"
				enableSelectByExample="false"
				selectByExampleQueryId="false" />
				
	</context>

</generatorConfiguration>
```

7. 使用插件生成所需必要文件（双击运行mybatis-generator:generate）
![[Pasted image 20230626115559.png]]
生成结果：
![[Pasted image 20230626120823.png]]

8. 编写测试代码

> `StuMapper.java` 注意：在接口顶部添加`@Mapper`注解，将当前接口声明为Mapper接口

```java
// 添加注解并定义抽象方法备用
@Select("select * from stu")
List<Stu> findAll();
```

> `StuService.java`

```java
package com.example.service;  
  
import com.example.domain.Stu;  
  
import java.util.List;  
  
public interface StuMapper {  
    List<Stu> findAll();
    Stu findById(int id);
}
```

> `StuServiceImpl.java`

```java
@Service
public class StuServiceIpml implements StuService {
	@Resource
	private StuMapper stuMapper;
	@Override
	public List<Stu> findAll() {
		return stuMapper.findAll();
	}
	@Override
	public Stu findById(int id) {
		System.out.println("IMPL findById is running...");
		return stuMapper.selectByPrimaryKey(id);
	}
}
```

9. 告知映射xml文件所在位置，在pom.xml的`<build>...</build>`标签下添加
```xml
<resources>
	<!-- 通知Maven编译SQL映射文件，是否Maven是不会编译SQL映射文件的 -->
	<resource>
		<directory>src/main/java</directory>
		<includes>
			<include>**/*.xml</include>
		</includes>
	</resource>
</resources>
```

10. 测试
	1. `http://localhost:8081/findById?id=1`
	2. `http://localhost:8081/findAll`

## 3. MyBatis事务支持

> 步骤

1. 创建SpringBoot模块

2. 选择起步依赖：lombok / MyBatis / web / MySQL Driver

3. 创建数据表与实体类
- `user表`
```sql
create table `user` (
	`id` int(10) unsigned not null auto_imcrement,
	`username` varchar(16) not null,
	`password` varchar(32) not null,
	`status` enum('0', '1', '2') default '2',
	primary key (`id`),
	unique key `username` (`username`)
) engine=InnoDB default charset=utf8;
```
- `com.example.domain.User 实体类`
```java
package com.example.domain;  
  
import lombok.Data;  
  
@Data  
public class User {  
    private int id;  
    private String username;  
    private String password;  
    private String status;  
}
```

4. 编写application配置文件
- `application.properties`
```properties
# 配置端口号  
server.port=8081  
  
# 数据库连接池配置  
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver  
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/spring_data?useUnicode=true&characterEncoding=utf8&useSSL=false  
spring.datasource.username=root  
spring.datasource.password=root
```

5. 编写程序代码
- `com.example.mapper.UserMapper`
```java
package com.example.mapper;  
  
import com.example.domain.User;  
import org.apache.ibatis.annotations.Insert;  
import org.apache.ibatis.annotations.Mapper;  
import org.apache.ibatis.annotations.Select;  
  
import java.util.List;  
  
@Mapper  
public interface UserMapper {  
    @Select("select * from `user`")  
    List<User> findAll();  
  
    @Insert("insert into `user` (`username`, `password`, `status`) values (#{username}, #{password}, #{status})")  
    void insert(User user);  
}
```

- `com.example.service.UserService`
```java
package com.example.service;  
  
import com.example.domain.User;  
  
import java.util.List;  
  
public interface UserService {  
    List<User> findAll();  
    void add(User user);  
}
```
- `com.example.service.impl.UserServiceImpl`
```java
package com.example.service.impl;  
  
import com.example.domain.User;  
import com.example.mapper.UserMapper;  
import com.example.service.UserService;  
import org.springframework.stereotype.Service;  
import org.springframework.transaction.annotation.Transactional;  
  
import javax.annotation.Resource;  
import java.util.List;  
  
@Service  
public class UserServiceImpl implements UserService {  
    @Resource  
    private UserMapper userMapper;  
    @Override  
    public List<User> findAll() {  
        return userMapper.findAll();  
    }  
  
    @Override  
    @Transactional // 事务注解
    public void add(User user) {  
        System.out.println("add() is running...");  
        userMapper.insert(user);  
        // System.out.println(10 / 0); // 制造一个异常
        user.setUsername(user.getUsername() + "New");  
        userMapper.insert(user);  
    }  
}
```

- `com.example.controller.UserController`
```java
package com.example.controller;  
  
import com.example.domain.User;  
import com.example.service.UserService;  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.ResponseBody;  
import org.springframework.web.bind.annotation.RestController;  
  
import javax.annotation.Resource;  
import java.util.List;  
  
// @Controller 
// @ResponseBody  
@RestController // @RestController = @Controller + @ResponseBody  
public class UserController {  
    @Resource  
    private UserService userService;  
  
    @RequestMapping("/findAll")  
    // @ResponseBody  
    public List<User> findAll() {  
        return userService.findAll();  
    }  
  
    @RequestMapping("/add")  
    // @ResponseBody  
    public String add(User user) {  
        userService.add(user);  
        return "添加成功";  
    }  
}
```

6. 测试
> `http://localhost:8081/findAll`
> `http://localhost:8081/add?username=Ben&password=333333&status=2`

> [!注意]
> `1. 如果一个业力操作中有一个以上的数据更新操作，就要开始事务`
> `2. 使用事务的数据包格式必须是InnoDB格式`

## 4. MyBatis-Plus整合步骤

1. 创建SpringBoot项目

2. 添加MySQL驱动

3. 手动引入依赖
```xml
<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>mybatis-plus-boot-starter</artifactId>  
    <version>3.5.3.1</version>  
</dependency>  
<dependency>  
    <groupId>org.projectlombok</groupId>  
    <artifactId>lombok</artifactId>  
</dependency>
```

4. 创建数据表与实体类
- `数据表略`
- `Stu.java实体类`
```java
package com.example.domain;  
  
import lombok.Data;  
  
@Data  
public class Stu {  
    private int id;  
    private String name;  
    private String sex;  
    private int age;  
    private double score;  
    private String tel;  
    private String classId;  
}
```

6. 编写`application`配置文件
```xml
spring:  
  datasource:  
    driver-class-name: com.mysql.cj.jdbc.Driver  
    url: jdbc:mysql:///spring_data?useUnicode=true&characterEncoding=utf8&useSSL=false  
    username: root  
    password: root  
  
mybatis-plus:  
  configuration:  
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl # 开启SQL日志  
    map-underscore-to-camel-case: true # 该配置就是将带有下划的表字段映射为驼峰格式的实体类属性  
  mapper-locations: classpath:mapper/*Mapper.xml  
  type-aliases-package: com.example.domain
```

6. 编写dao或mapper/纯注解开发
- `com.example.mapper.StuMapper.java`
```java
package com.example.mapper;  
  
import com.baomidou.mybatisplus.core.mapper.BaseMapper;  
import com.example.domain.Stu;  
import org.apache.ibatis.annotations.Mapper;  

// MyBatisPlus默认会将实体类属性的驼峰名转化为下划线去组织SQL语句
@Mapper  
public interface StuMapper extends BaseMapper<Stu> {
}
```

- `com.example.service.StuServiceImpl.java`
```java
package com.example.service;  
  
import com.example.domain.Stu;  
import com.example.mapper.StuMapper;  
import org.springframework.stereotype.Service;  
  
import javax.annotation.Resource;  
import java.util.List;  
  
@Service  
public class StuServiceImpl {  
    @Resource(name="stuMapper")  
    private StuMapper stuMapper;  
    public List<Stu> findAll() {  
        return stuMapper.selectList(null);  
    }  
}
```

7. 编写测试

```java
package com.example;  
  
import com.example.domain.Stu;  
import com.example.service.StuServiceImpl;  
import org.junit.jupiter.api.Test;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.test.context.SpringBootTest;  
  
import java.util.List;  
  
@SpringBootTest  
class SpringBoot09MyBatisPlusApplicationTests {  
    @Autowired  
    private StuServiceImpl stuService;  
  
    @Test  
    void finAllTest() {  
        List<Stu> stuList = stuService.findAll();
        for (Stu stu : stuList) {  
            System.out.println(stu);  
        }  
    }  
}
```


# 八、SpringBoot中的Web

## 1. 常用注解

注解 | 说明
:- | :- 
`@Controller` | Spring MVC的注解，处理http请求
`@RestController` | 是`@Controller`注解功能的增强，是`@Controller`和`@ResponseBody`的组合注解
`@RequestMapping` | 支持Get请求，也支持Post请求
`@GetMapping` | Get请求，多用于查询
`@PostMapping` | Post请求，多用于添加
`@PutMapping` | 本质上是Post请求，多用于修改
`@DeleteMapping` | 本质上是Post请求，多用于删除

- `com.example.controller.TestController.java`
```java
package com.example.controller;

import org.springframework.web.bind.annotation.*;

@RestController
public class TestController {
    @RequestMapping("/request")
    public String request() {
        return "request~";
    }

    @GetMapping("/get")
    public String get() {
        return "get~";
    }

    @PostMapping("/post")
    public String post() {
        return "post~";
    }

    @PutMapping("/put")
    public String put() {
        return "put~";
    }

    @DeleteMapping("/delete")
    public String delete() {
        return "delete~";
    }
}
```

- `static/index.html`
```html
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <title>Test</title>  
</head>  
<body>  
	<form action="/request">  
	    <button>request</button>  
	</form>  
	<hr/>  
	<form action="/get" method="get">  
	    <button>get</button>  
	</form>  
	<hr/>  
	<form action="/post" method="post">  
	    <button>post</button>  
	</form>  
	<hr/>  
	<form action="/put" method="post">  
	    <input type="hidden" name="_method" value="put"/>  
	    <button>put</button>  
	</form>  
	<hr/>  
	<form action="/delete" method="post">  
	    <input type="hidden" name="_method" value="delete"/>  
	    <button>delete</button>  
	</form>  
</body>  
</html>
```

- `application.properties`
```properties
spring.mvc.hiddenmethod.filter.enabled=true # 开启隐藏域的过滤  
```

## 2. SpringBoot实现RESTFul

> 常规地址访问风格：`http://localhost:8080/lesson?price=1299&days=150`
> RESEFul风格：`http://localhost:8080/lesson/1299/150`

### 案例

- `TestController.java`
```java
@RequestMapping("/restful/{teacher}/{lesson}")  
public String restful(@PathVariable String teacher, @PathVariable String lesson) {  
    return "teacher=" + teacher + "<br/>" + "lesson=" + lesson;  
}
```

- `index.html`
```html
<form action="/restful/Jack/SpringBoot">  
    <button>restful</button>  
</form>
```

> [!注意]
> `1.使用RESTFul风格后，不能将参数直接传递到对象中`
> `2.{}中的部分为动态参数，请求时必须填写`
> `3.使用RESTFul风格是，容易出现冲突，比如：/restful/Jack/JavaSE和/restful/JavaSE/Jack`
> `4.如果形参名与动态数名不一致，需要在@PathVariable("动态参数名")矫正动态参数名`


# 九、SpringBoot整合Redis

## 1. 整合步骤

1. 新建SpringBoot工程
![[Pasted image 20230627155105.png]]
2. 引入起步依赖
3. 配置Redis相关属性
```properties
spring.redis.host=127.0.0.1  
spring.redis.port=6379
```
4. 注入RedisTemplate模板并测试
```java
package com.example;  
  
import org.junit.jupiter.api.Test;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.test.context.SpringBootTest;  
import org.springframework.data.redis.core.RedisTemplate;  
import org.springframework.data.redis.core.StringRedisTemplate;  
  
@SpringBootTest  
class SpringBoot11RedisApplicationTests {  
    @Autowired  
    private RedisTemplate<String, String> redisTemplate;  
    @Autowired  
    private StringRedisTemplate stringRedisTemplate;  
  
    @Test  
    void teacherWriteAndRead() {  
        redisTemplate.boundValueOps("teacher").set("MrTom");  
        // String teacher = redisTemplate.boundValueOps("teacher").get();  
        String teacher = redisTemplate.boundValueOps("teacher").get(0, -1);  
        System.out.println(teacher);  
    }  
  
    @Test  
    void studentWriteAndRead() {  
        stringRedisTemplate.boundValueOps("student").set("Lily");  
        String student = stringRedisTemplate.boundValueOps("student").get();  
        System.out.println(student);  
    }  
}
```

## 2. RedisTemplate常用方法

https://juejin.cn/post/7096347622759202853

## 3. Redis or MySQL

1. 创建SpringBoot项目，并添加起步依赖（Lombok / SpringWeb / MySQL Driver / Spring Data Redis (Access + Driver)）
2. `pom.xml`中添加`MyBatisPlus`启动依赖
```xml
<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>mybatis-plus-boot-starter</artifactId>  
    <version>3.5.3.1</version>  
</dependency>
```
3. `application.properties`中添加对应的Redist、Datasource和MybatisPlus配置
```properties
server.port=8081  
# Redis  
spring.redis.host=127.0.0.1  
spring.redis.port=6379  
# datasource  
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver  
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/spring_data?useUniCode=true&characterEncoding=utf8&useSSL=false  
spring.datasource.username=root  
spring.datasource.password=root  
# Mybatis plus  
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl  
mybatis-plus.configuration.map-underscore-to-camel-case=true  
mybatis-plus.mapper-locations=classpath:mapper/*Mapper.xml  
mybatis-plus.type-aliases-package=com.example.domain
```
4. 创建`com.example.domain.User.java`实体类
```java
package com.example.domain;  
  
import lombok.AllArgsConstructor;  
import lombok.Data;  
import lombok.ToString;  
  
import java.io.Serializable;  
  
@Data  
@AllArgsConstructor  
@ToString  
public class User implements Serializable { // 记得实现一下序列化接口Serializable
    private Integer id;  
    private String username;  
    private String password;  
    private String status;  
}
```
5. 创建 Service
- `com.example.service.UserService.java`
```java
package com.example.service;  
  
import com.example.domain.User;  
  
import java.util.List;  
  
public interface UserService {  
    List<User> findAll();  
}
```
- `com.example.service.impl.UserServiceImpl.java`
```java
package com.example.service.impl;  
  
import com.example.domain.User;  
import com.example.mapper.UserMapper;  
import com.example.service.UserService;  
import org.springframework.data.redis.core.RedisTemplate;  
import org.springframework.data.redis.serializer.StringRedisSerializer;  
import org.springframework.stereotype.Service;  
  
import javax.annotation.Resource;  
import java.util.List;  
  
@Service  
public class UserServiceImpl implements UserService {  
    @Resource  
    private RedisTemplate<String, List<User>> redisTemplate;  
  
    // 定义Redis的数据格式对象（确定序列化方式）  
    private final StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();  
  
    @Resource  
    private UserMapper userMapper;  
  
    @Override  
    public List<User> findAll() {  
        // 设置key的序列化类型为String类型  
        redisTemplate.setKeySerializer(stringRedisSerializer);  
        // 在Redis中获取数据  
        List<User> usersAll = redisTemplate.opsForValue().get("usersAll");  
        // 如果在Redis中没有获取到数据，则从MySQL中获取并写入Redis中  
        if (null == usersAll) {  
            // 同步语句块，以防Redis中没数据时多人同时并发去MySQL中读取（防止缓存击穿）  
            synchronized (this) {  
	            // 重新在Redis中获取
                usersAll = redisTemplate.opsForValue().get("usersAll");   
                if (null == usersAll) { // 重新判断是否在Redis中拿到  
                    System.out.println("从MySQL中获取并写入Redis");  
                    usersAll = userMapper.selectList(null);  
                    redisTemplate.opsForValue().set("usersAll", usersAll);  
                }  
            }  
        }  
        return usersAll;  
    }  
}
```
6. 创建Controller
- `com.example.controller.UserController.java`
```java
package com.example.controller;  
  
import com.example.domain.User;  
import com.example.service.impl.UserServiceImpl;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
import javax.annotation.Resource;  
import java.util.List;  
  
@RestController  
public class UserController {  
    @Resource  
    private UserServiceImpl userServiceImpl;  
  
    @RequestMapping("/findAll")  
    public List<User> findAll() {  
        return userServiceImpl.findAll();  
    }  
}
```
7. 测试
`http://localhost:8081/findAll`

## 4. 集群设置

1. 搭建集群环境
- `redis-9527` - 主配置文件
```shell
include /etc/redis/redis.conf
daemonize yes
port 9527
pidfile /var/run/redis-9527.pid
logfile /home/maqf/myRedisData/redis-9527.log
dir /home/maqf/myRedisData
requirepass maqf
masterauth maqf
protected-mode no
```
- `redis-9528 - 从配置文件
```shell
include /etc/redis/redis.conf
daemonize yes
port 9528
pidfile /var/run/redis-9528.pid
logfile /home/maqf/myRedisData/redis-9528.log
dir /home/maqf/myRedisData
requirepass maqf
masterauth maqf
protected-mode no
slaveof 127.0.0.1 9527
```
- `redis-9529 - 从配置文件
```shell
include /etc/redis/redis.conf
daemonize yes
port 9529
pidfile /var/run/redis-9529.pid
logfile /home/maqf/myRedisData/redis-9529.log
dir /home/maqf/myRedisData
requirepass maqf
masterauth maqf
protected-mode no
slaveof 127.0.0.1 9527
```

- `sentinel-9527.conf` - 哨兵配置文件
```shell
# bind 127.0.0.1 :: 1 # 注释掉
protected-mode no
port 29527
sentinel monitor mymaster 127.0.0.1 9527 2
sentinel auth-pass mymaster maqf
```
- `sentinel-9528.conf` - 哨兵配置文件
```shell
# bind 127.0.0.1 :: 1 # 注释掉
protected-mode no
port 29528
sentinel monitor mymaster 127.0.0.1 9527 2
sentinel auth-pass mymaster maqf
```
- `sentinel-9529.conf` - 哨兵配置文件
```shell
# bind 127.0.0.1 :: 1 # 注释掉
protected-mode no
port 29529
sentinel monitor mymaster 127.0.0.1 9527 2
sentinel auth-pass mymaster maqf
```

2. 启动集群
```bash
sudo redis-server redis-9527.conf
sudo redis-server redis-9528.conf
sudo redis-server redis-9529.conf
sudo redis-server sentinel-9527.conf --sentinel
sudo redis-server sentinel-9528.conf --sentinel
sudo redis-server sentinel-9529.conf --sentinel
```

3. 修改`application`.properties`配置文件
```properties
# Redis（单台配置）
#spring.redis.host=127.0.0.1  
#spring.redis.port=9527

# 配置Redis集群
# 配置哨兵的连接地址，程序需要连接到哨兵，利用服务器获取Redis的主服务器
spring.redis.sentinel.nodes=127.0.0.1:29527,127.0.0.1:29528,127.0.0.1:29529
# 配置集群名称，名称来自哨兵的配置文件
spring.redis.sentinel.master=mymaster
# 配置主服务器密码
spring.redis.password=maqf
```


# 十、SpringBoot非Web项目

> 没有Web 依赖的项目就是非Web项目

## 1. 实现方式一

1. 新建SpringBoot工程，无需添加任何起步依赖
2. 编写程序代码
- `com.example.service.TestService.java`
```java
package com.example.service;

public interface TestService {
    void test();
}
```
- `com.example.service.impl.TestServiceImpl.java`
```java
package com.example.service.impl;  
  
import com.example.service.TestService;  
import org.springframework.stereotype.Service;  
  
@Service  
public class TestServiceImpl implements TestService {  
    @Override  
    public void test() {  
        System.out.println("test service~");  
    }  
}
```
- `com.example.SpringBoot13Webless01Application.java`
```java
package com.example;  
  
import com.example.service.impl.TestServiceImpl;  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
import org.springframework.context.ConfigurableApplicationContext;  
  
@SpringBootApplication  
public class SpringBoot13Webless01Application {  
  
   public static void main(String[] args) {  
      ConfigurableApplicationContext app = SpringApplication.run(SpringBoot13Webless01Application.class, args);  
      TestServiceImpl testService = app.getBean(TestServiceImpl.class);  
      testService.test();  
   }  
  
}
```

## 2. 实现方式二

1. 新建SpringBoot工程，无需添加任何起步依赖
2. 编写程序代码
- `com.example.service.TestService.java`同上
- `com.example.service.impl.TestServiceImpl.java`同上
-  `com.example.SpringBoot13Webless01Application.java`
```java
package com.example;  
  
import com.example.service.TestService;  
import org.springframework.boot.CommandLineRunner;  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
import javax.annotation.Resource;  

@SpringBootApplication  
public class SpringBoot14Webless02Application implements CommandLineRunner {  
    @Resource  
    private TestService testService;  
  
    public static void main(String[] args) {  
        SpringApplication.run(SpringBoot14Webless02Application.class, args);  
    }  
  
    @Override  
    public void run(String... args) throws Exception {  
        testService.test();  
    }  
}
```


# 十一、SpringBoot拦截器

> SpringMVC中的拦截器回顾

```xml
<!-- 配置拦截器 -->
<mvc:interceptors>
	<mvc:interceptor>
		<mvc:mapping path="/test01"/>
		<bean class="com.example.interceptor.MyInterceptor"/>
	</mvc:interceptor>
</mvc:interceptors>
```

**SpringBoot中拦截器的使用**

1. 新建SpringBoot项目，并添加SpringWeb起步依赖
2. 编写代码
- `com.example.controller.TestController.java`
```java
package com.example.controller;  
  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
@RestController  
public class TestController {  
    @RequestMapping("/test01")  
    public String test01() {  
        return "test01() is running~";  
    }  
  
    @RequestMapping("/test02")  
    public String test02() {  
        return "test02() is running~";  
    }  
  
    @RequestMapping("/test03")  
    public String test03() {  
        return "test03() is running~";  
    }  
}
```
- `com.example.interceptor.Myinterceptor.java`
```java
package com.example.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

// 拦截规则
public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle() is running~");
        return false;
    }
}
```
- `com.example.conf.MyInterceptorConf.java`
```java
package com.example.conf;

import com.example.interceptor.MyInterceptor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.lang.reflect.Array;
import java.util.ArrayList;

@Configuration
public class MyInterceptorConf implements WebMvcConfigurer {
    // 拦截规则配置
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        InterceptorRegistration myInterceptor = registry.addInterceptor(new MyInterceptor());
        // 添加过滤地址
        ArrayList<String> pathPatterns = new ArrayList<>();
        pathPatterns.add("/**");
        myInterceptor.addPathPatterns(pathPatterns);
        // 添加排除过滤地址
        ArrayList<String> exPathPatterns = new ArrayList<>();
        exPathPatterns.add("/test02");
        myInterceptor.excludePathPatterns(exPathPatterns);
    }
}
```
3. 启动测试
`http://localhost:8081/test01`


# 十二、SpringBoot使用Servlet

## 1. 实现方式一

1. 新建SpringBoot模块，选择SpringWeb起步依赖
2. 编写代码
- `com.example.servlet.TestServlet.java`
```java
package com.example.servlet;  
  
import javax.servlet.ServletException;  
import javax.servlet.annotation.WebServlet;  
import javax.servlet.http.HttpServlet;  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import java.io.IOException;  
  
@WebServlet("/testServlet")  
public class TestServlet extends HttpServlet {  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        this.doPost(req,resp);  
    }  
    @Override  
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        resp.getWriter().print("<h1>test servlet~</h1>");  
    }  
}
```
- `com.example.SpringBoot16ServletApplication.java`
```java
package com.example;  
  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
import org.springframework.boot.web.servlet.ServletComponentScan;  

@SpringBootApplication  
@ServletComponentScan(basePackages = "com.example.servlet") // 扫描servlet  
public class SpringBoot16ServletApplication {  
  
    public static void main(String[] args) {  
        SpringApplication.run(SpringBoot16ServletApplication.class, args);  
    }  
  
}
```

## 2. 实现方式二

1. 新建SpringBoot模块，选择SpringWeb起步依赖
2. 编写代码
-  `com.example.servlet.TestServlet.java`同上
- `com.example.conf/MyServletConf.java`用于配置Servlet，将配置好的Servlet放到Spring容器里
```java
package com.example.conf;

import com.example.servlet.TestServlet;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

// 使用这种方式则不需要扫描servlet
@Configuration
public class MyServletConf {
    @Bean
    public ServletRegistrationBean<TestServlet> myServletTest() {
        return new ServletRegistrationBean<>(new TestServlet(), "/testServlet");
    }
}
```


# 十三、SpringBoot使用filter

> 一般情况下使用拦截器取代

## 1. 实现方式一

1. 创建SpringBoot模块，并添加web依赖
2. 编写代码
- `com.example.controller.TestController.java`
```java
package com.example.controller;  
  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
@RestController  
public class TestController {  
    @RequestMapping("/test01")  
    public String test01() {  
        return "test01() is running~";  
    }  
  
    @RequestMapping("/test02")  
    public String test02() {  
        return "test02() is running~";  
    }  
  
    @RequestMapping("/test03")  
    public String test03() {  
        return "test03() is running~";  
    }  
}
```
- `com.example.filter.MyFilter.java`
```java
package com.example.filter;  
  
import javax.servlet.*;  
import javax.servlet.annotation.WebFilter;  
import java.io.IOException;  
  
@WebFilter("/*")  
public class MyFilter implements Filter {  
    @Override  
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {  
        System.out.println("doFilter() is running...");  
        filterChain.doFilter(servletRequest, servletResponse);  
    }  
}
```
- `com.example.SpringBoot17FilterApplication.java`
```java
package com.example;  
  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
import org.springframework.boot.web.servlet.ServletComponentScan;  

@SpringBootApplication  
@ServletComponentScan(basePackages = {"com.example.filter"})  
public class SpringBoot17FilterApplication {  
  
    public static void main(String[] args) {  
        SpringApplication.run(SpringBoot17FilterApplication.class, args);  
    }  
  
}
```
3. 测试

## 2. 实现方式二

1. 创建SpringBoot模块，并添加web依赖
2. 编写代码
- `com.example.controller.TestController.java`同上
- `com.example.filter.MyFilter.java`
```java
package com.example.filter;  
  
import javax.servlet.*;  
import javax.servlet.annotation.WebFilter;  
import java.io.IOException;  

public class MyFilter implements Filter {  
    @Override  
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {  
        System.out.println("doFilter() is running...");  
        filterChain.doFilter(servletRequest, servletResponse);  
    }  
}
```
- `com.example.conf.MyFilterConf.java`
```java
package com.example.conf;  
  
import com.example.filter.MyFilter;  
import org.springframework.boot.web.servlet.FilterRegistrationBean;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
@Configuration  
public class MyFilterConf {  
    @Bean  
    public FilterRegistrationBean<MyFilter> myFilterTest() {  
        FilterRegistrationBean<MyFilter> myFilter = new FilterRegistrationBean<>(new MyFilter());  
        myFilter.addUrlPatterns("/test01");  
        return myFilter;  
    }  
}
```
- `com.example.SpringBoot17FilterApplication.java`
```java
package com.example;  
  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
import org.springframework.boot.web.servlet.ServletComponentScan;  

@SpringBootApplication
public class SpringBoot17FilterApplication {  
  
    public static void main(String[] args) {  
        SpringApplication.run(SpringBoot17FilterApplication.class, args);  
    }  
  
}
```
3. 测试


# 十四、SpringBoot打包部署

## 1. WAR打包

1. 创建SpringBoot项目，并选择Web起步依赖
2. 创建项目时选择`war`包，打包方式
3. 编写测试用控制器代码
4. 通过IDEA自带打包工具进行打包（双击package打包）
![[Pasted image 20230630110739.png]]
5. 将打包好的`war`包文件改名，并放到tomcat的家目录中
6. 浏览器端输入地址测试

## 2. JAR打包

1. 创建SpringBoot模块，并选择`Web`起步依赖
2. 选择`jar`包，打包方式
3. 编写测试控制器代码
4. 测试运行并使用打包工具打包
5. 将打好的jar包复制到任意你喜欢的位置
6. 通过`java -jar xxx.jar`运行`jar`包，或者编写启动脚本，方便日后使用


# 十五、SpringBoot Session

![[Pasted image 20230630142521.png]]

> SpringBoot Session的功能是解决在负载均衡过程中，交互过程中SessionID不一致导致的Session丢失问题

**解决方案**

1. 使用Nginx中的IPHash负截均衡策略，但这种方式会导致服务器压力过载（不推荐）
2. 使用Tomcat容器的插件来实现Session的共享（将Session存入Redis中），但是由于Web依赖容器，因此每次更新服务器都需要配置插件
3. 利用SpringSession来实现，它将Session存入到Redis中，不依赖于容器，也不需要修改程序代码即可实现Session共享（推荐）

> 创建项目的时候添加`SpringSession`、`SpringWeb`、`Redis`起步依赖

`application.properties`
```properties
spring.redis.host=127.0.0.1
spring.redis.port=6379
# spring redis.password=

server.port=8080

# 设置网站访问地址
# 同域名下，同项目下的Session共享，适用于Nginx的负载均衡生产环境
server.servlet.context-path=/maqf

# 设置cookie的根路径，同域名下，不同项目的Session共享
server.servlet.session.cookie.path=/

# 同根域名，不同子域名Session共享
server.servlet.session.cookie.domain=myweb.com
```


# 十六、SpringBoot健康监测

> 检测SpringBoot的运行状态
> 创建项目的时候添加SpringBoot Actuator起步依赖即可

- `application.properties`
```properties
management.endpoints.web.exposure.include=*
```


# 十七、Thymeleaf

> SpringBoot推荐使用的模板引擎，同类产品还有Freemarker等

![[Pasted image 20230630175537.png]]

## 1. Thymeleaf 表达式

**实例**
1. 创建SpringBoot模块，添加SpringWeb/Thymeleaf/lombok/devtools起步依赖
2. 编辑`application.properties`配置文件
```properties
# 开发阶段，建议关闭thymeleaf的缓存，不然没有办法看到实时页面  
spring.thymeleaf.cache=false  
# 去掉HTML的语法验证  
spring.thymeleaf.mode=HTML
```
3. 创建实体类代码`com.example.domain.User.java`
```java
package com.example.domain;  
  
import lombok.AllArgsConstructor;  
import lombok.Data;  
  
@Data  
@AllArgsConstructor  
public class User {  
    public String username;  
    public Integer age;  
}
```
4. 创建控制器代码`com.example.controller.TestController.java`
```java
package com.example.controller;  
  
import com.example.domain.User;  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.ResponseBody;  
  
@Controller  
public class TestController {  
    @RequestMapping("/test01")  
    public String test01(Model model) {  
        model.addAttribute("lesson", "Java");  
        model.addAttribute("user", new User("Rose", 19));  
        return "test01";  
    }  
  
    @RequestMapping("/test")  
    @ResponseBody  
    public String test() {  
        return "test() is running...";  
    }  
}
```
5. 创建页面代码
```html
<!DOCTYPE html>  
<html lang="en" xmlns:th="https://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>test01</title>  
</head>  
<body>  
  
<h1>标准变量 - 表达式</h1>  
<p>lesson is <span th:text="${lesson}"></span></p>  
<p>username is <span th:text="${user.username}"></span></p>  
<p>age is <span th:text="${user.age}"></span></p>  
<p>username is <span th:text="${user.getUsername()}"></span></p>  
<p>age is <span th:text="${user.getAge()}"></span></p>  
  
<hr/>  
  
<h1>选择变量 - 表达式</h1>  
<span th:object="${user}">  
    <p>username is <sapn th:text="*{username}"></sapn></p>  
    <p>age is <sapn th:text="*{age}"></sapn></p>  
    <p>username is <sapn th:text="*{getUsername()}"></sapn></p>  
    <p>age is <sapn th:text="*{getAge()}"></sapn></p>  
</span>  
  
<hr/>  
  
<h1>URL - 表达式</h1>  
<a th:href="@{https://www.baidu.com}">广域网绝对路径</a><br/>  
<a th:href="@{/test}">项目的绝对路径</a><br/>  
<a th:href="@{test}">项目的相对路径</a><br/>  
  
</body>  
</html>
```
6. 测试`localhost:8080/test01`

## 2. Thymeleaf 常见属性

属性 | 说明
:- | :-
`th:action` | 类似`<form>`标签的action属性，主要结合URL表达式，获取动态变量
`th:method` | 设置请求方式，post/get;
`th:href` | 定义超链接，主要结合URL表达式获取动态变量
`th:src` | 用于外部资源引入
`th:id` | 类似于普通的id属性
`th:name` | 类似于普通的name属性
`th:value` | 用于设置表单中的value值
`th:attr` | 设置表单元素自定义的任意属性键值对
`th:text` | 动态获取文本属性
`th:onclick` | 用于点击事件
`th:style` | 类似于普通的style属性
`th:each` | 迭代器，遍历数组，集合...
`th:if / th:unless` | 条件标签，unless与if相反
`th:switch / th:case` |
`th:line` | 
`...` |

### 1) URL相关

- `TestController.java`
```java
@RequestMapping("urlTest")  
public String urlTest(Model model) {  
    model.addAttribute("actionUrl", "/test");  
    model.addAttribute("hrefUrl", "https://baidu.com");  
    model.addAttribute("srcUrl", "/avatar.png");  
    return "urlTest";  
}
```
- `urlTest.html`
```html
<!DOCTYPE html>  
<html lang="en" xmlns:th="https://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>Title</title>  
</head>  
<body>  
  <form th:action="${actionUrl}">  
    <button>action</button>  
  </form>  <hr/>  <a th:href="${hrefUrl}+'/'">href</a>  
  <hr/>  <img th:src="${srcUrl}" alt="no img" />  
</body>  
</html>
```

### 2) 表单设置默认值

- `TestController.java`
```java
@RequestMapping("/edit")  
public String edit(Model model) {  
    model.addAttribute("user", new User("root", 0));  
    return "edit";  
}
```
- `edit.html`
```html
<!DOCTYPE html>
<html lang="en" xmlin="https://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<table>
    <form th:action="@{/update}">
        <tr>
            <td>用户名：</td>
            <td>
                <input type="text" name="username" th:value="${user.getUsername()}"/>
            </td>
        </tr>
        <tr>
            <td>年龄：</td>
            <td>
                <input type="number" name="age" th:value="${user.getAge()}"/>
            </td>
        </tr>
        <tr>
            <td colspan="2">
                <input type="submit" value="提交"/>
            </td>
        </tr>
    </form>
</table>
</body>
</html>
```

### 3) 遍历

#### a. 遍历List集合

- `TestController.java`
```java
@RequestMapping("/each")  
public String each(Model model) {  
    ArrayList<User> users = new ArrayList<>();  
    users.add(new User("Lily", 19));  
    users.add(new User("Robby", 29));  
    model.addAttribute("users", users);  
    return "each";  
}
```
- `each.html`
```html
<!DOCTYPE html>  
<html lang="en" xmlin="https://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>Title</title>  
</head>  
<body>  
  <table border="1">  
    <tr>      <th>序号</th>  
      <th>名字</th>  
      <th>年龄</th>  
    </tr>    <tr th:each="user:${users}">  
      <td th:text="${userStat.count}"></td>  
      <td th:text="${user.getUsername()}"></td>  
      <td th:text="${user.getAge()}"></td>  
    </tr>  </table></body>  
</html>
```

#### b. 遍历Map集合

- `TestController.java`
```java
@RequestMapping("/eachMap")  
public String eachMap(Model model) {  
    HashMap<String, String> userMap = new HashMap<>();  
    userMap.put("Jack", "28");  
    userMap.put("Rose", "38");  
    model.addAttribute("userMap", userMap);  
    return "userMap";  
}
```
- `eachMap.index`
```html
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <title>userMap</title>  
</head>  
<body>  
<table border="1">  
  <tr>    <th>序号</th>  
    <th>键</th>  
    <th>值</th>  
  </tr>  <tr th:each="item:${userMap}">  
    <td th:text="${itemStat.count}"></td>  
    <td th:text="${item.getKey()}"></td>  
    <td th:text="${item.getValue()}"></td>  
  </tr></table>  
</body>  
</html>
```

#### c. 遍历数组

- `TestController.java`
```java
@RequestMapping("/eachArr")  
public String eachArr(Model model) {  
    User[] users = new User[3];  
    users[0] = new User("Tom", 16);  
    users[1] = new User("Lily", 17);  
    users[2] = new User("Ken", 18);  
    model.addAttribute("userArr", users);  
    return "userArr";  
}
```
- `eachArr.html`
```html
<!DOCTYPE html>  
<html lang="en" xmlns:th="https://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>userArr</title>  
</head>  
<body>  
<table border="1">  
  <tr>    <th>序号</th>  
    <th>名字</th>  
    <th>年龄</th>  
  </tr>  <tr th:each="user:${userArr}">  
    <td th:text="${userStat.count}"></td>  
    <td th:text="${user.getUsername()}"></td>  
    <td th:text="${user.getAge()}"></td>  
  </tr></table>  
</body>  
</html>
```

### 4) 选择属性

#### a. `th:if`

- `TestController.java`
```java
@RequestMapping("/ifTest")  
public String ifTest(Model model) {  
    ArrayList<User> users = new ArrayList<>();  
    users.add(new User("Lily", 17));  
    users.add(new User("Rose", 18));  
    model.addAttribute("users", users);  
    return "ifTest";  
}
```
- `ifTest.html`
```html
<!DOCTYPE html>  
<html lang="en" xmlns:th="https://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>ifTest</title>  
</head>  
<body>  
<table>  
  <tr>    <th>序号</th>  
    <th>名字</th>  
    <th>年龄</th>  
  </tr>  <tr th:each="user:${users}">  
    <td th:text="${userStat.count}"></td>  
    <td th:text="${user.getUsername()}"></td>  
    <td th:if="${user.getAge() >= 18}">成年</td>  
    <td th:if="${user.getAge() < 18}">未成年</td>  
  </tr></table>  
</body>  
</html>
```

#### b. `th:switch / th:case`

- `TestController.java`
```java
@RequestMapping("/switchTest")  
public String switchTest(Model model) {  
    int gender = 0;  
    model.addAttribute("gender", gender);  
    return "switchTest";  
}
```
- `switchTest.html`
```html
<!DOCTYPE html>  
<html lang="en" xmlns:th="https://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>switchTest</title>  
</head>  
<body>  
  <span th:switch="${gender}">  
    <p th:case="0">female</p>  
    <p th:case="1">male</p>  
  </span></body>  
</html>
```

### 5) 内联属性

- `inline.html`
```html
<script th:inline="text">
	alert("[[${name}]]")
</script>
<script th:inline="javascript">
	alert([[${name}]])
</script>
```


## 3.表达式对象

对象名 | 说明
:- | :-
`#dates` | Java.util.Date对象的实用方法
`#calendars` | 和dates类似，Java.util.Calendar对象
`#numbers` | 格式化数字对象的实用方法
`#strings` | 字符串对象的实用方法：contains, startsWith, prepending/appending等
`#objects` | 对object操作的实用方法
`#bools` | 对布尔值求值的实用方法
`#arrays` | 数组的实用方法
`#lists` | list的实用方法
`#sets` | set的实用方法
`#maps` | map的实用方法
`#aggregates` | 对数组或集合创建聚合的实用方法

```html
<p th:text="${#session.getAttribute('mySession')}"></p>
<p th:text="${#request.getServletPath()}"></p>
<p th:text="${#dates.format(nowDate, 'yyyy-mm-dd hh:mm:ss')}"></p>
<p th:text="${#numbers.formatCurrency(price)}"></p>
<p th:text="${#strings.substring(myStr, 3, 8)}"></p>
```

