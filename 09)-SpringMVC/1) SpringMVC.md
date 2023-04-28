
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
            <version>5.1.5.RELEASE</version>
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
  
import com.example.global.User;  
import org.junit.Test;  
import org.junit.runner.RunWith;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.jdbc.core.BeanPropertyRowMapper;  
import org.springframework.jdbc.core.JdbcTemplate;  
import org.springframework.test.context.ContextConfiguration;  
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;  
  
import java.util.List;  
import java.util.Map;  
  
@RunWith(SpringJUnit4ClassRunner.class)  
@ContextConfiguration("classpath:ApplicationContext.xml")  
public class MyJdbcTemplateCRUDTest {  
  
    @Autowired  
    private JdbcTemplate jdbcTemplate;  
  
    @Test  
    public void updateTest() {  
        int row = jdbcTemplate.update("update `user` set `username`=? where `username`=?", "Rose", "Lily");  
        System.out.println(row + "行受影响！");  
    }  
  
    @Test  
    public void deleteTest() {  
        int row = jdbcTemplate.update("delete from `user` where username=?", "test");  
        System.out.printf("%d行受影响", row);  
    }  
  
    @Test  
    public void insertTest() {  
        int row = jdbcTemplate.update("insert into `user` (`username`, `password`) value (?, ?)", "Kitty", "888");  
        System.out.printf("%d行受影响", row);  
    }  
  
    @Test  
    public void selectTest() {  
        List<User> query = jdbcTemplate.query("select * from `user`", new BeanPropertyRowMapper<>(User.class));  
        System.out.println(query);  
    }  
  
    @Test  
    public void selectByIDTest() {  
        Map<String, Object> user = jdbcTemplate.queryForMap("select * from `user` where `id`=?", 1);  
        System.out.println(user);  
        System.out.println(user.get("username"));  
    }  
  
    @Test  
    public void selectByUsernameTest() {  
        User user = jdbcTemplate.queryForObject("select * from `user` where `username`=?", new BeanPropertyRowMapper<>(User.class), "Rose");  
        if (null == user) return;  
        System.out.println(user);  
        System.out.println(user.getUsername());  
    }  
  
    @Test  
    public void selectCountTest() {  
        Integer total = jdbcTemplate.queryForObject("select count(*) from `user`", Integer.class);  
        System.out.printf("一共有%d用户", total);  
    }  
}
```


# 九、SpringMVC拦截器

> SpringMVC中的拦截器（interceptor）类似于Servlet中的Filter，用于对处理器进行预处理或后处理
> 可以将拦截器按一定的顺序连接成一条链，这条链称之为**拦截器链（Interceptor Chain）**，在访问被拦截的方法或者字段的时候，拦截器链中的拦截器会按之前的定义顺序被调用，拦截器也是AOP（面向切面编程）思想的具体体现

## 1. Filter 与 Interceptor

区别 | 过滤器（Filter）| 拦截器（Interceptor）
:- | :- | :-
使用范围 | 是Servlet规范中的一部分，任何的JavaWeb工程都可以使用 | 是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用
拦截范围 | 在url-parttern中配置了/\*之后，可以对索要访问的资源进行拦截 | 如果访问的是jsp、html、css、image或者是js文件则不会进行拦截

## 2. 快速上手

1. 创建 一个项目环境

![[Pasted image 20230329173254.png]]

2. 创建Controller

```java
package com.maqf.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class TestController {
    @RequestMapping("/runTest")
    public ModelAndView runTest(ModelAndView modelAndView, String username) {
        System.out.println("runTest() is running...");
        modelAndView.addObject("username", username);
        modelAndView.setViewName("success.jsp");
        return modelAndView;
    }
}
```

4. 创建拦截器

```java
package com.maqf.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyInterceptor01 implements HandlerInterceptor {
    @Override
    // 执行目标之前被执行
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle() is running...");
        if (request.getParameter("username").equals("root")) {
            return true; // 放行
        } else {
            request.getRequestDispatcher("/fail.jsp").forward(request, response);
            return false; // 不放行
        }
    }

    @Override
    // 执行目标之后，在返回视图之前
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        // HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
        System.out.println("postHandle() is running...");
    }

    @Override
    // 最后执行
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
        System.out.println("afterCompletion() is running...");
    }
}
```

4. 配置拦截器

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
    <context:component-scan base-package="com.maqf.controller"/>
    <mvc:annotation-driven/>

    <!-- 配置拦截器 -->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.maqf.interceptor.MyInterceptor01"/>
        </mvc:interceptor>
    </mvc:interceptors>

    <mvc:default-servlet-handler/>
</beans>
```


# 十、SpringMVC异常处理

## 1. 异常处理方式

1. 使用SpringMVC提供的简单异常处理器SimpleMappingExceptionResolver
2. 实现Spring的异常处理接口HandlerExceptionResolver自定义自己的异常处理器

## 2. 方案一

> SimpleMappingExceptionResolver
> 在SpringMVC配置文件中添加

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
    <context:component-scan base-package="com.example.controller"/>
    <mvc:annotation-driven/>

    <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <!-- 配置默认的异常跳转视图页面 -->
        <property name="defaultErrorView" value="defaultException.jsp"></property>
        <property name="exceptionMappings">
            <map>
                <entry key="java.lang.ArithmeticException" value="ArithmeticException.jsp"/>
                <entry key="java.lang.NullPointerException" value="NullPointerException.jsp"/>
                <entry key="com.example.exception.MyException" value="MyException.jsp"/>
            </map>
        </property>
    </bean>
</beans>
```

## 3. 方案二

> HandlerExceptionResolver

1. 创建自定义异常处理的实现类，实现 `HandlerExceptionResolver`

```java
package com.example.resolver;

import com.example.exception.MyException;
import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class ExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("ExceptionResolver.jsp");
        if (ex instanceof NullPointerException) {
            modelAndView.addObject("info", "NullPointerException");
        } else if (ex instanceof ArithmeticException) {
            modelAndView.addObject("info", "ArithmeticException");
        } else if (ex instanceof MyException) {
            modelAndView.addObject("info", "MyException");
        }
        return modelAndView;
    }
}
```

2. 配置异常处理器，添加内容到SpringMVC配置文件

```xml
<bean class="com.example.resolver.ExceptionResolver"/>
```

3. 编写异常页面

```jsp
<%--
  Created by IntelliJ IDEA.
  User: Administrator
  Date: 2023/4/27
  Time: 18:02
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Exception Resolver</title>
</head>
<body>
  <h1>exception: ${info}</h1>
</body>
</html>
```


# 十一、Spring-AOP

## 1. 简介

> AOP(Aspect Oriented Programming)-面向切面编程
> 是通过预编译方式和运行期间动态代理实现程序统一维护的技术

- 作用：在程序运行期间，在不修改源码的情况下对方法进行功能上的补充增强
- 优势：减少重复的代码，提高开发效率，便于维护

## 2. AOP相关概念

- Target（目标对象）：目标对象
- Proxy（代理）：代理对象
- Joinpoint（连接点）：就是被拦截到的方法（可以被增强的方法）
- Pointcut（切入点）：用来指定被设置的连接点
- Advice（通知/增强）：拦截到连接点之后要做的动作
- Aspect（切面）：切入点 + 通知
- Weaving（织入）：切入点与通知结合的过程

## 3. AOP开发注意事项

1. 需要编写的内容
	- 编写核心业务代码（目标类的目标方法）
	- 编写切面类，切面类中有通知（增强功能方法）
	- 在配置文件中，配置织入关系，就是将通知与连接点相结合
2. AOP技术实现
	- 做好相关的配置之后，让Spring快乐的帮我们做事情
3. AOP底层使用的代理方式
	- JDK代理：如果目标类没有使用接口
	- cjlib代理：如果目标类使用了接口

## 4. 快速上手

### a. XML方式实现

1. 导入pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>SpringDemo-08-AOP</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>SpringDemo-08-AOP</name>
    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.source>1.8</maven.compiler.source>
        <junit.version>5.9.1</junit.version>
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
            <version>5.3.26</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.3.26</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.6</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
        </dependency>
    </dependencies>
</project>
```

2. 创建Target类

```java
package com.example.aop;

public class Target implements TargetInterface {
    @Override
    public void test() {
        System.out.println("test() is running~");
    }
}
```

3. 创建MyAspect类

```java
package com.example.aspect;

public class MyAspect {
    public void before() {
        System.out.println("before() is running~");
    }
    public void after() {
        System.out.println("after() is running~");
    }
}
```

4. 配置ApplicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
">

    <bean id="target" class="com.example.aop.Target"/>
    <bean id="myAspect" class="com.example.aspect.MyAspect"/>

    <!-- 配置织入过程 -->
    <aop:config>
        <aop:aspect ref="myAspect">
            <aop:before method="before" pointcut="execution(public void com.example.aop.Target.test())"/>
            <aop:after method="after" pointcut="execution(public void com.example.aop.Target.test())"/>
        </aop:aspect>
    </aop:config>

</beans>
```

5. 创建测试类

```java
package com.example.test;

import com.example.aop.TargetInterface;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:ApplicationContext.xml")
public class AopRunTest {
    @Autowired
    private TargetInterface target;

    @Test
    public void test() {
        target.test();
    }
}
```


---

#### 切点表达式

`execution[修饰符] 返回值类型 包名.类名.方法名(参数类型)`
- 权限修饰符可以省略
- 返回值、包名、类名、方法名都可以使用\*表示任意
- 包名与类名之间使用一个点儿，表示当前包下的类；两个点儿，表示当前包及子包下的类
- 参数列表可以使用两个点儿，表示任意个数，任意类型的参数（用于方法的重载）

```xml
execution(public void com.example.aop.Target.method())
<!-- 指定一个绝对方法的路径 -->
execution(void com.example.aop.Target.*(..))
<!-- void com.example.aop.Target.任意方法(任意参数) -->
execution(* com.example.aop.*.*(..))
<!-- 任意返回值 com.example.aop.任意类.任意方法(任意参数) -->
execution(* com.example.aop..*.*(..))
<!-- 任意返回值 com.example.aop..aop包及其子包的任意类，任意方法(任意参数) -->
execution(* *..*.*(..))
<!-- 各种任意 -->
```

#### 通知类型

通知配置语法：`<aop:通知类型 method:"切面类中的方法名" pointcut="切点表达式"/>`

名称 | 标签 | 说明
:- | :- | :-
前置通知 | `<aop:before>` | 指定目标方法执行之前
后置通知 | `<aop:returning>` | 指定目标方法执行之后
环绕通知 | `<aop:around>` | 指定目标方法执行之前、之后都执行；`ProceedingJoinPoint`作为形参
异常抛出通知 | `<aop:after-throwing>` | 指定目标方法出现异常时
最终通知 | `<aop:after>` | 各种情况都会执行

```java
package com.example.aspect;

import org.aspectj.lang.ProceedingJoinPoint;

public class MyAspect {
    public void before() {
        System.out.println("before() is running~");
    }
    public void after() {
        System.out.println("after() is running~");
    }
    public Object around(ProceedingJoinPoint point) throws Throwable {
        System.out.println("around before~");
        Object resObj = point.proceed(); // 调用的是目标对象当中需要增强的目标方法
        System.out.println("around after~");
        return resObj;
    }
}
```

```xml
<!-- 配置织入过程 -->
<aop:config>
	<aop:aspect ref="myAspect">
		<aop:before method="before" pointcut="execution(public void com.example.aop.Target.test())"/>
		<aop:after method="after" pointcut="execution(public void com.example.aop.Target.test())"/>
		<aop:around method="around" pointcut="execution(* com.example.aop.Target.*(..))"/>
	</aop:aspect>
</aop:config>
```

#### 切点的抽取

```xml
<!-- 配置织入过程 -->
<aop:config>
	<!-- 切点的抽取 -->
	<aop:pointcut id="myPointCut" expression="execution(* com.example.aop.Target.*(..))"/>
	<!-- 声明切面 -->
	<aop:aspect ref="myAspect">
		<!-- 切面 = 切点 + 通知 -->
		<aop:before method="before" pointcut="execution(public void com.example.aop.Target.test())"/>
		<aop:after method="after" pointcut="execution(public void com.example.aop.Target.test())"/>
		<aop:around method="around" pointcut-ref="myPointCut"/>
	</aop:aspect>
</aop:config>
```


### b. 注解方式实现

1. 创建Target类，使用`@Component`暴露出去

```java
package com.example.anno;

import org.springframework.stereotype.Component;

@Component("target")
public class Target implements TargetInterface {
    @Override
    public void test() {
        System.out.println("anno test() is running~");
    }
}
```

2. 创建MyAspectAnno类，使用`@Component`暴露并使用`@Aspect`声明切面类

```java
package com.example.anno;

import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class MyAspectAnno {
    @Before("execution(public void com.example.anno.Target.test())")
    public void before() {
        System.out.println("anno before() is running~");
    }

    @After(value = "execution(public void com.example.anno.*.*(..))")
    public void after() {
        System.out.println("anno after() is running~");
    }
}
```

3. 创建`ApplicationContextAnno.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
">
    <!-- 扫描 -->
    <context:component-scan base-package="com.example.anno"/>
    <!-- 自动代理声明切面 -->
    <aop:aspectj-autoproxy/>
</beans>
```

> 注意：Target类 和 MyAspectAnno类都要在com.example.anno包中被扫描到

4. 创建测试类

```java
package com.example.test;

import com.example.anno.TargetInterface;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:ApplicationContextAnno.xml")
public class AnnoRunTest {
    @Autowired
    private TargetInterface target;

    @Test
    public void test() {
        target.test();
    }
}
```

#### 注解通知类型

语法：@通知注解("切点表达式")

名称 | 标签 | 说明
:- | :- | :-
前置通知 | `@Before` | 提定目标方法执行之前
后置通知 | `@AfterReturning` | 指定目标方法执行之后
环绕通知 | `@Around` | 指定目标方法执行之前、之后都执行；`ProceedingJoinPoint`作为形参
异常抛出通知 | `@AfterThrowing` | 指定目标方法出现异常时
最终通知 | `@After` | 各种情况都会执行

#### 切点表达式的抽取

```java
package com.example.anno;

import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class MyAspectAnno {
	// 抽取
    @Pointcut("execution(public void com.example.anno.Target.test())")
    public void pointcut() {}

    // @Before("execution(public void com.example.anno.Target.test())")
    // @Before("pointcut()")
    @Before("MyAspectAnno.pointcut()")
    public void before() {
        System.out.println("anno before() is running~");
    }

    @After(value = "execution(public void com.example.anno.*.*(..))")
    public void after() {
        System.out.println("anno after() is running~");
    }
}
```


# 十二、Spring事务处理

## 1. 编程式事务控制相关对象

> 了解

- `PlatformTransactionManager`接口-平台事务管理器（Sring的事务管理器）-维护事务的相关参数

方法 | 说明
:- | :-
`TransactionStatus getTransaction(TransactionDefinition defination)` | 获取事务的状态信息
`void commit(TransactionStatus status)` | 提交事务
`void rollback(TransactionStatus status)` | 回滚事务

- `TransactionDefinition`是事务的定义信息对象

方法 | 说明
:- | :-
`int getIsolationLevel()` | 获取事务的隔离级别
`int getPropogationBehavior()` | 获取事务的传播行为
`int getTimeout()` | 获取超时时间
`boolean isReadOnly()` | 是否只读，true只读，false读写

> 隔离级别：用于解决多个并发问题，其中包括脏读、不可重复读和幻读

级别 | 状态 | 说明
:- | :- | :-
READ UNCOMMITTED | 读未提交数据 | 允许事务读取未被其他事务提交的变更数据，<br/>会出现脏读，不可重复和幻读问题
READ COMMITTED | 读已提交数据 | 只允许事务读取已经被其他事务提交的变更数据，<br/>可避免脏读，仍会出现不可重复读和幻读问题
REPEATABLE READ (MySQL默认)  | 可重复读 | 确保事务可以多次从一个字段中读取相同的值，<br/>在此事务持续期间，禁止其他事务对此字段的更新，<br/>可以避免脏读和不可重复读，仍会出现幻读问题
SERIALIZABLE | 序列化 | 锁表，确保事务可以从一个表中读取相同的行，<br/>在这个事务持续期间，禁止其他事务对该表执行插入，<br/>更新和删除操作，可避免所有并发问题，但性能非常低

> 传播行为：用于解决业务层调用多个动作的时候，统一事务处理

行为 | 说明
:- | :-
REQUIRED | 如果当前没有事务，就新建一个事务，如果已存在一个事务中，加入到这个事务中（默认值）
SUPPORTS | 支持当前事务，如果当前没有事务，就以非事务方式执行（没有事务）
MANDATORY | 使用当前事务，如果当前没有事务，就抛异常
REQUERS_NEW | 新建事务，如果当前在事务中，就把当前事务挂起
NOT_SUPPORTED | 以非事务方式执行操作，如果当前存在事务，就把当前事务持起
NEVER | 以非事务方式运行，如果当前存在事务，则抛异常
NESTED | 如果当前存在事务，在嵌套事务内执行，如果当前没有事务，则执行REQUIRED类似操作
超时时间 | 默认值是-1，没有超时限制，如果有，以秒为单位设置
是否只读 | 建议查询时设置为只读

- `TransactionStatus`事务的运行状态

方法 | 说明
:- | :-
`boolean hasSavepoint()` | 是否存储回滚点
`boolean isCompleted()` | 事务是否完成
`boolean isNewTransaction()` | 是否是新的事务
`boolean isRollbackOnly()` | 事务是否回滚


## 2. 声明式事务控制

> 使用声明的方式来处理事务
> 事务处理通常是系统层面的处理，与业务层分离。维护起来更加的方便
> 实际上就是AOP思想的另外一种体现形式

### a. 准备测试环境

1. 

