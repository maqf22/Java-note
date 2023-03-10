
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

- 返回引用数据类型

1. 代码中直接返回要返回的对象

```java
@RequestMapping("/test12")
@ResponseBody
public User test12() {
	return new User("root", "123");
}
```

2. 在SpringMVC.xml中添加支持

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
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 包扫描 -->
    <context:component-scan base-package="com.maqf.controller"/>

    <!-- 加载SpringMVC中三大组件，包括：处理映射器，处理适配器，视图解析器 -->
    <!-- 其中默认底层也继承了Jackson相关转换功能 -->
    <mvc:annotation-driven/>

</beans>
```

3. pom.xml中添加Jackson的依赖关系

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-databind</artifactId>
	<version>2.14.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-core</artifactId>
	<version>2.14.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations -->
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-annotations</artifactId>
	<version>2.14.1</version>
</dependency>
```

- 手动处理json串的另一种获取方式

```java
@RequestMapping("/test13")
@ResponseBody
public String test13() throws JsonProcessingException {
	ObjectMapper om = new ObjectMapper();
	User root = new User("root", "123");
	String s = om.writeValueAsString(root);
	return s;
}
```


# 五、SpringMVC获取请求参数

## 1. 基本类型参数

```java
@RequestMapping("/test14")
@ResponseBody
public void test14(HttpServletRequest request) {
	String username = request.getParameter("username");
	String password = request.getParameter("password");
	System.out.println(username + ": " + password);
}

@RequestMapping("/test15")
@ResponseBody
public void test15(String username, String password) {
	System.out.println(username + ": " + password);
}
```


## 2. POJO类型参数

> （Plain Ordinary java Object）实际就是普通JavaBean

确保User类中存在setter/getter

```java
@RequestMapping("/test16")
@ResponseBody
public void test16(User user) {
	String username = user.getUsername();
	String password = user.getPassword();
	System.out.println(username + ": " + password);
}
```


## 3. 数组类型

```jsp
<form action="/web/test17" method="get">
    <input type="checkbox" name="hobby" value="0">
    <input type="checkbox" name="hobby" value="1">
    <input type="checkbox" name="hobby" value="2">
    <button>submit</button>
</form>
```

```java
@RequestMapping("/test17")
@ResponseBody
public void test17(String[] hobby) {
	for (String index : hobby) {
		System.out.println(index);
	}
}
```


## 4. 集合类型参数

### a. 通过表单发送请求

```java
package com.maqf.global;

import java.util.List;

/**
 * @ClassName MyValueObject
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class MyValueObject {
    private List<User> userList;

    public List<User> getUserList() {
        return userList;
    }

    public void setUserList(List<User> userList) {
        this.userList = userList;
    }

    @Override
    public String toString() {
        return "MyValueObject{" +
                "userList=" + userList +
                '}';
    }
}
```

```jsp
<form action="/web/test18" method="get">
    <input type="hidden" name="userList[0].username" value="Jack">
    <input type="hidden" name="userList[0].password" value="123">
    <input type="hidden" name="userList[1].username" value="Rose">
    <input type="hidden" name="userList[1].password" value="456">
    <button>submit</button>
</form>
```

```java
@RequestMapping("/test18")
@ResponseBody
public void test18(MyValueObject mvo) {
	System.out.println(mvo);
}
```

### b. 通过Ajax请求

```jsp
<script>  
    function method() {  
        let arr = [  
            {  
                username: "Lily",  
                password: "123"  
            },  
            {  
                username: "Roll",  
                password: "456"  
            }  
        ];  
        let arrJson = JSON.stringify(arr);  
        let xmlHttpRequest = new XMLHttpRequest();  
        xmlHttpRequest.onreadystatechange = () => {  
            if (xmlHttpRequest.readyState === 4 && xmlHttpRequest.status === 200) {  
                console.log("request success!")  
            }  
        }  
        xmlHttpRequest.open("POST", "/web/test19");  
        xmlHttpRequest.setRequestHeader("Content-Type", "application/json");  
        xmlHttpRequest.send(arrJson);  
    }  
</script>
```

```java
@RequestMapping(value = "/test19", method = RequestMethod.POST)
@ResponseBody
public void test19(@RequestBody List<User> list) {
	System.out.println(list);
}
```

### c. @RequestParam

> 参数绑定：用于解决前后端参数名不一致问题 

```jsp
<a href="/web/test20?uname=root&passwd=123">test20</a>
```

```java
@RequestMapping("/test20")
@ResponseBody
public void test20(@RequestParam(value = "uname", required = false, defaultValue = "admin") String username, @RequestParam("passwd") String password) {
	System.out.println(username + ": " + password);
}
```

- 注解参数
	- value：请求参数名称
	- required：必须包含，默认值为true
	- defaultValue：设置参数的默认值

### d. @PathVariable

> 获取Restful风格的参数，集合@RequestMapping实现
> 通过”URL + 请求方式”来表示请求的目的，结合：POST(C) / GET(R) / PUT(U) / DELETE(D)

```jsp
<a href="/web/test21/admin">test21</a>
```

```java
@RequestMapping(value = "/test21/{username}")
@ResponseBody
public void test21(@PathVariable("username") String username) {
	System.out.println("username: " + username);
}
```

### e. @RequestHeader

> 用于获取请求数据，相当于`request.getHeader(name)`

- 属性
	- value：请求头的名字
	- required：是否必须携带此参数

```jsp
<a href="/web/test22/">test22</a>
```

```java
@RequestMapping("/test22")
@ResponseBody
public void test22(@RequestHeader("host") String host) {
	System.out.println(host);
}
```

### f. @CookieValue

> 用于获取请求头中的Cookie

- 属性
	- value：cookie名字
	- required：是否必须携带此cookie

```java
@RequestMapping("/test23")
@ResponseBody
public void test23(@CookieValue("JSESSIONID") String JSESSIONID) {
	System.out.println(JSESSIONID);
}
```


# 六、自定义类型转换器

- SpringMVC为我们提供了一下常用的类型转换器，可以满足我们非特殊情况下的类型转换
- 在特殊情况下，默认的类型转换器不能满足我们需求的时候，可以使用自定义类型转换器来解决问题 

1. 定义转换器类实现Converter接口

```java
package com.maqf;

import org.springframework.core.convert.converter.Converter;

import java.text.ParseException;

import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * @ClassName DateConverter
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class DateConverter implements Converter<String, Date> {

    @Override
    public Date convert(String s) {
        Date date = null;
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-mm-dd");
        try {
            date = simpleDateFormat.parse(s);
        } catch (ParseException e) {
            throw new RuntimeException(e);
        }
        return date;
    }
}
```

2. 在配置文件中声明转换器

```xml


    <!-- 声明自定义类型转换器 -->
    <bean id="dateConverter" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <list>
                <bean class="com.maqf.DateConverter"></bean>
            </list>
        </property>
    </bean>
```

3. 在`<annotation-driven>`中引用转换器

```xml
<mvc:annotation-driven conversion-service="dateConverter"/>
```


# 七、文件上传

## 1. 客户端三要素

1. 表单属性`<input type="file"/>`
2. 表单提交方式`method="post"`
3. 表单的额外提交属性`enctype="multipart/form-data"`

> 当表单提交方式为多部分表单模式时，request对象中的各种getXXX()方法将失效


## 2. 单文件上传

1. `pom.xml`中导入`fileupload`和`io`相关坐标

```xml
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.3.1</version>
</dependency>

<dependency>
	<groupId>commons-io</groupId>
	<artifactId>commons-io</artifactId>
	<version>2.4</version>
</dependency>
```

2. `SpringWebMVC.xml`中配置文件上传解析器

```xml
<!-- 配置文件上传解析器 -->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<property name="defaultEncoding" value="UTF-8"/>
	<property name="maxUploadSize" value="10240000"/>
</bean>
```

3. 编写文件上传前后端代码

```jsp
<form action="/web/test26" method="post" enctype="multipart/form-data">
    <input type="text" name="username" placeholder="请输入用户名"/>
    <input type="file" name="uploadFile"/>
    <input type="submit" value="upload"/>
</form>
```

```java
@RequestMapping(value = "/test26", method = RequestMethod.POST)
@ResponseBody
public void test26(String username, MultipartFile uploadFile) throws IOException {
	// 获取文件对象名字
	String fileName = username + "-" + uploadFile.getOriginalFilename();
	// 将文件转移到指定的File对象中
	uploadFile.transferTo(new File("D:\\uploadFileTest\\" + fileName));
}
```


## 3. 多文件上传

### a. 场景1

```jsp
<form action="/web/test27" method="post" enctype="multipart/form-data">
    <input type="text" name="username" placeholder="请输入用户名"/>
    <input type="file" name="uploadFile01"/>
    <input type="file" name="uploadFile02"/>
    <input type="submit" value="upload"/>
</form>
```

```java
@RequestMapping(value = "/test27", method = RequestMethod.POST)
@ResponseBody
public void test27(String username, MultipartFile uploadFile01, MultipartFile uploadFile02) throws IOException {
	// 获取文件对象名字
	String fileName01 = username + "-" + uploadFile01.getOriginalFilename();
	String fileName02 = username + "-" + uploadFile02.getOriginalFilename();
	// 将文件转移到指定的File对象中
	uploadFile01.transferTo(new File("D:\\uploadFileTest\\" + fileName01));
	uploadFile02.transferTo(new File("D:\\uploadFileTest\\" + fileName02));
}
```

### b. 场景2

```jsp
<form action="/web/test28" method="post" enctype="multipart/form-data">
    <input type="text" name="username" placeholder="请输入用户名"/>
    <input type="file" name="uploadFiles" multiple/>
    <input type="submit" value="upload"/>
</form>
```

```java
@RequestMapping(value = "/test28", method = RequestMethod.POST)
@ResponseBody
public void test28(String username, MultipartFile[] uploadFiles) throws IOException {
	for (MultipartFile uploadFile : uploadFiles) {
		String fileName = username + "-" + uploadFile.getOriginalFilename();
		System.out.println(fileName);
		uploadFile.transferTo(new File("D:\\uploadFileTest\\" + fileName));
	}
}
```

> 解决全局请求参数中文乱码问题，配置web.xml

```xml
<filter>
	<filter-name>CharacterEncodingFilter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	<init-param>
		<param-name>encoding</param-name>
		<param-value>UTF-8</param-value>
	</init-param>
</filter>
<filter-mapping>
	<filter-name>CharacterEncodingFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```


# 八、Spring JdbcTemplate

## 1. 快速上手

1. `pom.xml`中导入`spring-jdbc`与`spring-tx`坐标

```xml
<dependency>
	<groupId>springframework</groupId>
	<artifactId>spring-jdbc</artifactId>
	<version>1.2.6</version>
</dependency>

<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-tx</artifactId>
	<version>5.3.20</version>
</dependency>
```

2. 创建数据库与实体对应的类

> 创建User数据表，User类，保证结构一致

3. 创建JdbcTemplate对象

4. 执行CRUD相关数据库操作

```java
package com.example.test;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;
import org.springframework.jdbc.core.JdbcTemplate;

import java.beans.PropertyVetoException;

/**
 * @ClassName JdbcTemplateTest
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class JdbcTemplateTest {
    private static final String DRIVER = "com.jdbc.mysql.Driver";
    private static final String URL = "jdbc:mysql://127.0.0.1:3306/spring_data";
    private static final String USER = "root";
    private static final String PASSWORD = "root";

    @Test
    public void test01() throws PropertyVetoException {
        // 定义一个数据源
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass(DRIVER);
        dataSource.setJdbcUrl(URL);
        dataSource.setUser(USER);
        dataSource.setPassword(PASSWORD);
        // 定义JdbcTemplate对象
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        // 插入一条数据
        String sql = "insert into `user` (`username`, `password`) value (?, ?)";
        int row = jdbcTemplate.update(sql, new String[]{"Jack", "123"});
        System.out.println(row + "行受影响！");
    }
}
```


## 2. 通过IOC实现

1. ApplicationContext.xml中添加容器对象
``
```properties
JDBC_DRIVER=com.jdbc.mysql.Driver
JDBC_URL=jdbc:mysql://127.0.0.1:3306/spring_data
JDBC_USER=root
JDBC_PASSWORD=root
```

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
        <property name="user" value="${JDBC_USER}"/>
        <property name="password" value="${JDBC_PASSWORD}"/>
    </bean>

    <bean name="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

</beans>
```

2. 编写测试代码

```java
@Test
public void test02() {
	ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("ApplicationContext.xml");
	JdbcTemplate jt = app.getBean(JdbcTemplate.class);
	int row = jt.update("insert into `user` (`username`, `password`) value (?, ?)", new String[]{"Rose", "456"});
	System.out.println(row + "行受影响！");
}
```


## 3. 整合Junit测试CRUD

1. 导入Spring-test与Junit

> 注意坐标版本！版本不兼容会导致错误。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>SpringDemo-04-JdbcTemplate</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>SpringDemo-04-JdbcTemplate</name>
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
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>

        <dependency>
            <groupId>springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>1.2.6</version>
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

2. 新建MyJdbcTemplateCRUDTest.java测试CRUD

```java
package com.example.test;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

/**
 * @ClassName MyJdbcTemplateCRUDTest
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:ApplicationContext.xml")
public class MyJdbcTemplateCRUDTest {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Test
    public void updateTest() {
        int row = jdbcTemplate.update("update `user` set `username`=? where `username`=?", new String[]{"Rose", "Lily"});
        System.out.println(row + "行受影响！");
    }
}
```

