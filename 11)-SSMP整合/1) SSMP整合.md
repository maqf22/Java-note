
# 一、整合

> Spring + SpringMVC + MyBatis-Plus整合

==数据库及表的操作略过==

1. 创建一个JavaEE Module，目录如下

![[Pasted image 20230519104828.png]]

2. pom.xml配置坐标

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>SSMP</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>SSMP</name>
    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.source>1.8</maven.compiler.source>
        <spring.version>5.3.26</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.32</version>
        </dependency>
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.5</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.24</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp.jstl</groupId>
            <artifactId>javax.servlet.jsp.jstl-api</artifactId>
            <version>1.2.2</version>
        </dependency>
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
        </dependency>
        
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.3.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

3. log4j.properties配置

```properties
### direct log messages to stdout ###  
log4j.appender.stdout=org.apache.log4j.ConsoleAppender  
log4j.appender.stdout.Target=system.out  
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout  
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n  
  
### direct messages to file myLog.log ###  
log4j.appender.file=org.apache.log4j.FileAppender  
log4j.appender.file.File=c:/myLog.log  
log4j.appender.file.layout=org.apache.log4j.PatternLayout  
log4j.appender.file.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n  
### set log levels - for more verbose logging change 'info' to 'debug' ###  
log4j.rootLogger=debug, stdout
```

4. jdbc.properties配置

```properties
JDBC_DRIVER=com.mysql.cj.jdbc.Driver
JDBC_URL=jdbc:mysql://127.0.0.1:3306/spring_data?useSSL=true
JDBC_USERNAME=root
JDBC_PASSWORD=root
```

5. **ApplicationContext.xml配置Spring相关bean，并整合MyBatis-Plus**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
">
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${JDBC_DRIVER}"/>
        <property name="jdbcUrl" value="${JDBC_URL}"/>
        <property name="user" value="${JDBC_USERNAME}"/>
        <property name="password" value="${JDBC_PASSWORD}"/>
    </bean>

    <!-- 整合MyBatis-plus -->
    <bean id="sessionFactory" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置 Mapper 接口扫描 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.example.mapper"/>
    </bean>

    <!-- 配置注解扫描 -->
    <context:component-scan base-package="com.example"/>
</beans>
```

6. SpringWebMVC.xml配置SpringMVC相关

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
">
    <!-- 配置扫描路径 -->
    <context:component-scan base-package="com.example.controller"/>
    <!-- 注解驱动，导入三大组件 -->
    <mvc:annotation-driven/>
    <!-- 释放静态资源权限 -->
    <mvc:default-servlet-handler/>
</beans>
```

7. web.xml配置web层相关

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:ApplicationContext.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:SpringWebMVC.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

8. 创建Studnet实体类

```java
package com.example.domain;

import com.baomidou.mybatisplus.annotation.TableName;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
@TableName("stu")
public class Student {
    private int id;
    private String name;
    private String sex;
    private Integer age;
    private Float score;
    private String tel;
    private String classId;
}
```

9. 创建StuMapper接口

```java
package com.example.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.example.domain.Student;

public interface StuMapper extends BaseMapper<Student> {
}
```

> 测试

MyBatisPlusTest.java

```java
import com.example.domain.Student;
import com.example.mapper.StuMapper;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.List;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:ApplicationContext.xml")
public class MyBatisPlusTest {
    @Autowired
    private StuMapper stuMapper;

    @Test
    public void test01() {
        List<Student> students = stuMapper.selectList(null);
        for (Student student : students) {
            System.out.println(student);
        }
    }
}
```

10. StuService.java

```java
package com.example.service;

import com.example.domain.Student;
import com.example.mapper.StuMapper;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import java.util.List;

@Service("stuService")
public class StuService {
    @Resource
    private StuMapper stuMapper;

    public List<Student> getStuList() {
        return stuMapper.selectList(null);
    }
}
```

11. StuController.java

```java
package com.example.controller;

import com.example.domain.Student;
import com.example.service.StuService;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.annotation.Resource;
import java.util.List;

@Controller
public class StuController {
    @Resource
    private StuService stuService;

    @RequestMapping("/selAll")
    public String StuList(Model model) {
        List<Student> stuList = stuService.getStuList();
        model.addAttribute("stuList", stuList);
        return "index.jsp";
    }
}
```

12. index.jsp

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>JSP - Hello World</title>
</head>
<body>
<h1>Student Manager</h1>
<hr/>
<a href="/ssmp/selAll">select all student</a>
<hr/>
<c:if test="${stuList != null}">
    <table border="1">
        <tbody>
        <tr>
            <th>id</th>
            <th>name</th>
            <th>sex</th>
            <th>age</th>
            <th>score</th>
            <th>telephone</th>
            <th>class id</th>
        </tr>
        <c:if test="${stuList.size() == 0}">
            <tr>
                <td colspan="7">no data</td>
            </tr>
        </c:if>
        <c:forEach items="${stuList}" var="stu">
            <tr>
                <td>${stu.id}</td>
                <td>${stu.name}</td>
                <td>${stu.sex}</td>
                <td>${stu.age}</td>
                <td>${stu.score}</td>
                <td>${stu.tel}</td>
                <td>${stu.classId}</td>
            </tr>
        </c:forEach>
        </tbody>
    </table>
</c:if>
</body>
</html>
```


# 二、CUD功能实现

Student.java

```java
package com.example.domain;

import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableName;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
@TableName("stu")
public class Student {
    private Integer id;
    private String name;
    private String sex;
    private Integer age;
    private Float score;
    private String tel;
    @TableField("classId")
    private String classId;
}
```

StuController.java

```java
package com.example.controller;

import com.example.domain.Student;
import com.example.service.StuService;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.servlet.ModelAndView;

import javax.annotation.Resource;
import java.util.List;

@Controller
public class StuController {
    @Resource
    private StuService stuService;

    @RequestMapping("/selAll")
    public String getStuList(Model model) {
        List<Student> stuList = stuService.getStuList();
        model.addAttribute("stuList", stuList);
        return "index.jsp";
    }

    @RequestMapping(value = "/add", method = RequestMethod.POST)
    public ModelAndView addStu(ModelAndView modelAndView, Student stu) {
        int row = stuService.add(stu);
        System.out.printf("%d受影响", row);
        modelAndView.setViewName("/selAll");
        return modelAndView;
    }

    @RequestMapping("/new")
    public String add(Model model) {
        model.addAttribute("title", "add");
        return "edit.jsp";
    }

    @RequestMapping("/edit")
    public String edit(Model model, int id) {
        Student stu = stuService.getStuById(id);
        model.addAttribute("title", "edit");
        model.addAttribute("stu", stu);
        return "edit.jsp";
    }

    @RequestMapping(value = "/update", method = RequestMethod.POST)
    public ModelAndView update(ModelAndView modelAndView, Student stu) {
        int row = stuService.update(stu);
        System.out.printf("%d行受影响", row);
        modelAndView.setViewName("/selAll");
        return modelAndView;
    }

    @RequestMapping("/del")
    public ModelAndView del(ModelAndView modelAndView, int id) {
        int row = stuService.del(id);
        System.out.printf("%d行受影响", row);
        modelAndView.setViewName("/selAll");
        return modelAndView;
    }
}
```

StuService.java

```java
package com.example.service;

import com.example.domain.Student;
import com.example.mapper.StuMapper;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import java.util.List;

@Service("stuService")
public class StuService {
    @Resource
    private StuMapper stuMapper;

    public List<Student> getStuList() {
        return stuMapper.selectList(null);
    }

    public int add(Student stu) {
        return stuMapper.insert(stu);
    }

    public Student getStuById(int id) {
        return stuMapper.selectById(id);
    }

    public int update(Student stu) {
        return stuMapper.updateById(stu);
    }

    public int del(int id) {
        return stuMapper.deleteById(id);
    }
}
```

index.jsp

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
<h1>Student Manager</h1>
<hr/>
<a href="/ssmp/selAll">select all student</a>
<a href="/ssmp/new">add student</a>
<hr/>
<c:if test="${stuList != null}">
    <table border="1">
        <tbody>
        <tr>
            <th>id</th>
            <th>name</th>
            <th>sex</th>
            <th>age</th>
            <th>score</th>
            <th>telephone</th>
            <th>class id</th>
            <th>operate</th>
        </tr>
        <c:if test="${stuList.size() == 0}">
            <tr>
                <td colspan="8">no data</td>
            </tr>
        </c:if>
        <c:forEach items="${stuList}" var="stu">
            <tr>
                <td>${stu.id}</td>
                <td>${stu.name}</td>
                <td>${stu.sex}</td>
                <td>${stu.age}</td>
                <td>${stu.score}</td>
                <td>${stu.tel}</td>
                <td>${stu.classId}</td>
                <td>
                    <a href="/ssmp/edit?id=${stu.id}">编辑</a>
                    <a href="/ssmp/del?id=${stu.id}">删除</a>
                </td>
            </tr>
        </c:forEach>
        </tbody>
    </table>
</c:if>
</body>
</html>
```

edit.jsp

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%--
  Created by IntelliJ IDEA.
  User: Administrator
  Date: 2023/5/19
  Time: 13:34
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title></title>
</head>
<body>
    <h1>${title}</h1>
    <hr />
    <form action="/ssmp/${title == 'edit' ? 'update' : title}" method="post">
        <input type="text" name="id" value="${stu.id}" hidden />
        <table border="1">
            <tbody>
            <tr>
                <td>name</td>
                <td><input type="text" name="name" value="${stu.name}"/></td>
            </tr>
            <tr>
                <td>sex</td>
                <td>
                    <input type="radio" name="sex" value="1" ${stu.sex == '1' ? 'checked' : ''}/>male
                    <input type="radio" name="sex" value="2" ${stu.sex == '2' ? 'checked' : ''}/>female
                </td>
            </tr>
            <tr>
                <td>age</td>
                <td><input type="number" name="age" value="${stu.age}"/></td>
            </tr>
            <tr>
                <td>score</td>
                <td><input type="text" name="score" value="${stu.score}"/></td>
            </tr>
            <tr>
                <td>tel</td>
                <td><input type="text" name="tel" value="${stu.tel}"/></td>
            </tr>
            <tr>
                <td>class id</td>
                <td><input type="text" name="classId" value="${stu.classId}"/></td>
            </tr>
            </tbody>
        </table>
        <input type="submit" value="${title}"/>
    </form>
</body>
</html>
```


# 三、MyBatis-Plus CRUD

```java
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.conditions.update.UpdateWrapper;
import com.example.domain.Student;
import com.example.mapper.StuMapper;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:ApplicationContext.xml")
public class MyBatisPlusTest {
    @Autowired
    private StuMapper stuMapper;

    // select
    @Test
    public void test01() {
        List<Student> students = stuMapper.selectList(null);
        for (Student student : students) {
            System.out.println(student);
        }
    }

    // insert
    @Test
    public void test02() {
        Student student = new Student();
        student.setName("huang");
        student.setSex("2");
        student.setAge(33);
        student.setScore(73.0f);
        student.setTel("123");
        student.setClassId("none");
        int row = stuMapper.insert(student);
        System.out.printf("%d行受影响", row);
    }

    // update
    @Test
    public void test03() {
        Student student = new Student();
        student.setId(18);
        student.setName("huangGu");
        int row = stuMapper.updateById(student);
        System.out.printf("%d行受影响", row);
    }
    @Test
    public void test04() {
        Student student = new Student();
        student.setName("HuangLaoMa");
        student.setAge(100);
        QueryWrapper<Student> sqw = new QueryWrapper<>();
        sqw.eq("name", "huangGu").eq("age", 33);
        int row = stuMapper.update(student, sqw);
        System.out.printf("%d行受影响", row);
    }
    @Test
    public void test05() {
        UpdateWrapper<Student> suw = new UpdateWrapper<>();
        suw.set("name", "huangGu").set("age", 33).eq("name", "HuangLaoMa").eq("age", 100);
        int row = stuMapper.update(null, suw);
        System.out.printf("%d行受影响", row);
    }

    // delete
    @Test
    public void test06() {
        Student student = new Student();
        student.setId(13);
        student.setName("maqf");
        QueryWrapper<Student> sqw = new QueryWrapper<>(student);
        int row = stuMapper.delete(sqw);
        System.out.printf("%d行受影响", row);
    }
    @Test
    public void test07() {
        HashMap<String, Object> map = new HashMap<>();
        map.put("name", "test02");
        map.put("age", 23);
        int row = stuMapper.deleteByMap(map);
        System.out.printf("%d行受影响", row);
    }
    @Test
    public void test08() {
        ArrayList<Integer> arrayList = new ArrayList<>();
        arrayList.add(9);
        arrayList.add(18);
        int row = stuMapper.deleteBatchIds(arrayList);
        System.out.printf("%d行受影响", row);
    }

    // select
    @Test
    public void test09() {
        Student student = stuMapper.selectById(8);
        System.out.println(student);
    }
    // TODO...
}

```