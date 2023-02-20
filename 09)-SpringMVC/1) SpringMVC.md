
# 一、SpringMVC介绍

SpringMVC是一种Java实现MVC设计模式的请求驱动类型的轻量级Web框架


# 二、SpringMVC快速入门

> 需求：客户端发起请求、服务器端接收请求，执行逻辑并进行视图层的跳转

1. `pom.xml`中导入SpringMVC坐标

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.maqf</groupId>
    <artifactId>SpringDemo-03-WebMVC</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>SpringDemo-03-WebMVC</name>
    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.source>1.8</maven.compiler.source>
    </properties>

    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.15.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>5.1.15.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.15.RELEASE</version>
        </dependency>
    </dependencies>
    
</project>
```

2. `web.xml`配置SpringMVC前端控制器DispathcerServlet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- 配置全局初始化参数 -->
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

3. 创建Controller类和视图页面`main/java/com/maqf/controller/UserController.java`

```java
package com.maqf.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * @ClassName UserController
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
@Controller
// @RequestMapping("/user")
public class UserController {
    @RequestMapping("/test01")
    public String test01() {
        System.out.println("UserController test01 running~");
        return "success.jsp"; // 返回的字段符就是要跳转的页面文件名（相对当前访问地址）
        // return "/success.jsp"; // 返回的字段符就是要跳转的页面文件名（根目录）
        // return "redirect:success.jsp"; // 重定向
        // return "forward:success.jsp"; // 转发（默认）
    }
}
```

4. 使用注解配置Controller类中的业务方法的映射地址

> 源码中对应方法上添加`@RequestMapping("/test01")`

5. 配置SpringMVC核心配置文件`SpringWebMVC.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.maqf.controller"/>
    
    <!-- <context:component-scan base-package="com.maqf"> -->
    <!--     <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/> -->
    <!-- </context:component-scan> -->

	<!-- 自定义前后缀 -->
    <!-- <bean id="iVR" class="org.springframework.web.servlet.view.InternalResourceViewResolver"> -->
    <!--     <property name="prefix" value="/user/"/> -->
    <!--     <property name="suffix" value=".jsp"/> -->
    <!-- </bean> -->

</beans>
```

6. 向客户端发起请求

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>Hello World</title>
</head>
<body>
	<h1>MVC</h1>
	<hr>
	<a href="/web/test01">test01</a>
</body>
</html>
```


# 三、Spring组件

## 1. SpringMVC组件

1. 前端控制器：DispatcherServlet
2. 处理器映射器：HandlerMapping
3. 处理器适配器：HandlerAdapte
4. 处理器：Handler
5. 视图解析器：ViewResolver
6. 视图：View


## 2. 组件解析

`@RequestMapping` - 用于创建请求URL和处理方法之间的对应关系
- 添加位置
	- 类上方：请求URL的第一级访问目录，此处不写，相当于应用的根目录
	- 方法上方：请求URL的第二级访问目录，与类上方使用@RequestMapping标注的一级目录拼装成一个完整目录
- 属性
	- value：指定请求的URL地址（默认值）
	- method：指定请求方式
	- params:限制请求参数的条件，支持简单表达式写法，要求请求参数的键值对必须和配置相同

```java
package com.maqf.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

/**
 * @ClassName UserController
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
@Controller
// @RequestMapping("/user")
public class UserController {
    @RequestMapping("/test01")
    public String test01() {
        System.out.println("UserController test01 running~");
        return "success.jsp"; // 返回的字段符就是要跳转的页面文件名（相对当前访问地址）
        // return "/success.jsp"; // 返回的字段符就是要跳转的页面文件名（根目录）
        // return "redirect:success.jsp"; // 重定向
        // return "forward:success.jsp"; // 转发（默认）
    }

    @RequestMapping(value = "/test02", method={RequestMethod.POST})
    public String test02() {
        System.out.println("UserController test02 running~");
        return "success.jsp";
    }

    @RequestMapping(value = "/test03", params={"username", "password"})
    public String test03() {
        System.out.println("UserController test03 running~");
        return "success.jsp";
    }
}
```


# 四、SpringMVC数据响应

## 1. 页面跳转

- 直接返回字符串

> 这种方式将返回的字符串与视图解析器的前后缀拼接在一起之后进行跳转，可通过SpringWebMVC配置前后缀

```xml
<!-- 自定义前后缀 -->
<bean id="iVR" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="prefix" value="/user/"/>
	<property name="suffix" value=".jsp"/>
</bean>
```

- 通过ModelAndView 对象进行返回

> 通过ModelAndView对象来设置请求转发要携带的参数，视图页面文件等相关信息

```java
@RequestMapping("/test04")
public String test04(HttpServletRequest req) {
	System.out.println("UserController test04 running~");
	String username = req.getParameter("username");
	String password = req.getParameter("password");
	System.out.println(username);
	System.out.println(password);
	return "success.jsp";
}

@RequestMapping("/test05")
public void test05(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	System.out.println("UserController test04 running~");
	req.getRequestDispatcher("success.jsp").forward(req, resp);
}

@RequestMapping("/test06")
public ModelAndView test06(HttpServletRequest req)  {
	System.out.println("UserController test04 running~");
	ModelAndView modelAndView = new ModelAndView();
	modelAndView.addObject("username", req.getParameter("username"));
	modelAndView.addObject("password", req.getParameter("password"));
	modelAndView.setViewName("success.jsp");
	return modelAndView;
}

@RequestMapping("/test07")  
public ModelAndView test07(ModelAndView modelAndView)  {  
    modelAndView.addObject("username", "root");  
    modelAndView.addObject("password", "123");  
    modelAndView.setViewName("success.jsp");  
    return modelAndView;  
}  
  
@RequestMapping("/test08")  
public String test08(Model model)  {  
    model.addAttribute("username", "root");  
    model.addAttribute("password", "123");  
    return "success.jsp";  
}
```


## 2. 回写数据

- 直接返回字符串

```java
// 回写
@RequestMapping("/test09")
public void test09(HttpServletResponse response) throws IOException {
	response.setContentType("text/html;charset=utf-8");
	response.getWriter().println("Hello SpringMVC");
}

@RequestMapping(value = "/test10", produces = {"text/html;charset=utf-8"})
@ResponseBody // 注标当前的映射为响应体内容回写
public String test10() {
	return "hello mvc";
}
```

- json

> Json就是一对用大括号括起来的键值对的集合，用来传递对象

	通过控制器返回json串

```java
@RequestMapping("/test11")  
@ResponseBody  
public String test11() {  
    return "{\"username\": \"root\", \"password\": \"123\"}";  
}
```

	结合SpringMVC使用

```jsp
<script>
    (function () {
        let xmlHttpRequest = new XMLHttpRequest();
        xmlHttpRequest.onreadystatechange = () => {
            if (xmlHttpRequest.readyState === 4 && xmlHttpRequest.status === 200) {
                let res = xmlHttpRequest.responseText;
                console.log(JSON.parse(res));
            }
        };
        xmlHttpRequest.open("GET", "/web/test11");
        xmlHttpRequest.send();
    })();
</script>
```




