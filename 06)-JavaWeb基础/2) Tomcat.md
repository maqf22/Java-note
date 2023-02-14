
# 一、配置

- 需要在系统中配置JAVA_HOME和JRE_HOME环境变量

> PS：Tomcat是一个使用Java开发的服务软件，所以在运行时需要JDK或者JRE作为支持

- 需要在系统中配置CATALINA_HOME环境变量

> PS：这个环境变量指向的是Tomcat的安装目录，部分系统如果不配置该变量可能会导致Tomcat无法运行

- 启动与关闭
	- 进入Tomcat的安装目录下面的bin目录（bin目录中存放的都是可执行文件）
	- bin目录中有两个文件名分别为startup和shutdown的脚本文件用于启动和关闭tomcat服务
- 安装（卸载）到系统服务
	- 安装服务：service install
	- 卸载服务：service unistall
	- 查看服务： `win + r 输入 services.msc <Enter>


# 二、目录结构

- bin：相关命令文件
- conf：相关配置文件（比如端口号的配置就在该目录当中，默认为8080）
- lib：运行时要依赖的jar文件
- logs：运行时的日志文件
- temp：临时文件
- webapps：网站目录
- work：用来存储JSP文件编译后的class文件


# 三、IDEA的配置

1. 打开IDEA并创建一个JavaWeb项目
2. 配置Tomcat：File -> Setting -> Build,Execution,Deployment -> Application Services -> +(添加) -> Tomcat Server(默认会选中Tomcat的安装目录，前提已设置环境变量，如果没有手动选择)
3. 配置Tomcat的开关（开关用于在IDEA里面直接操作Tomcat）：Run -> Edit Configuration -> +(添加) -> Tomcat Server -> Local ->name起名 -> 设置默认浏览器 -> 选择JRE
4. 设置当前工程使用的JDK：File -> Project Structure -> Project -> 选择1.8
5. 解决IDEA控制台输出乱码问题：
	1. 修改idea配置文件：Help -> Edit Custom VM Options，在最后一行加入 -Dfile.encoding=UTF-8
	2. 修改项目的字符集：File -> Settings -> Editor -> File Encodings 三处都改为utf-8
	3. 重启IEDA


# 四、Servlet规范

- JavaWeb网站内部的目录结构
	- src 目录：用来存储动态资源文件，.java文件或相关的配置文件
		- 包目录.xxx.java
	- web 目录：用来储存静态资源文件，html/css/js，多媒体文件/jar包/核心的配置文件web.xml
		- WEB-INF 目录：只能用它来存储网站的核心配置文件web.xml
		- lib 目录：用于存储网站索要依赖的jar包
		- 网站的页面文件（静态文件都存储在web的根目录下）
	- JavaSE规范：Java标准开发规范，定制Java的基本语法
	- JavaEE规范：JavaEE是商业开发规范，定制了Java与不同服务器进行联合开发时所要遵循的规范，在JavaEE规范中，定制了Java与13种服务器联合使用要遵循的开发规则，比如：JDBC就是其中一种
	- Servlet也是这13种规范当中的其中一种，指定了Java和Http服务器之间的开发规则
	- 使用的Http服务器工具是Tomcat, Tomcat为Java提供了一个工具包，servlet-api.jar


# 五、案例

> 包含静态和动态资源文件的网站项目

- index.html 是一个网站的默认首页（不需要输入文件名，就会被服务器直接读取显示出来）
- Tomcat 默认能够识别的欢迎页面（默认展示页），配置内容在`Tomcat安装目录/conf/web.xml`

```xml
<welcome-file-list>
	<welcome-file>index.html</welcome-file>
	<welcome-file>index.htm</welcome-file>
	<welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

- 修改欢迎页面的默认文件名，可以修改本项目中WEB-INF目录中的web.xml
- 在网站中创建一个动态资源文件（.java）
	- 原生态做法：在src目录中创建一个普通的Java类，并继承HttpServlet类，重写HttpServlet中service方法，因为在前台对当前servlet进行访问时，servlet方法会被自动调用！修改项目中的核心配置文件，web.xml，需要在该配置文件中对新定义的servlet类进行注册和对应的映射设置，设置的具体方法

```xml
<!-- 注册Servlet，将Servlet类的地址告诉web网站 -->
<servlet>  
    <servlet-name>HelloServlet</servlet-name>  
    <servlet-class>com.servlet.demo.HelloServlet</servlet-class>  
</servlet>  
<!-- 设置Servlet类与访问地址之间的映射关系 -->
<servlet-mapping>  
    <servlet-name>HelloServlet</servlet-name>  
    <url-pattern>/a.do</url-pattern>  
    <!-- 必须以/开始，名字随意，建议以.do结尾 -->
</servlet-mapping>
```

	- 非原生的做法（IDEA给我提供的），与上面类似

- 在自已定义Servlet类当中重写父类方法时
	- 如果重写service相当于屏蔽了其他的，doGet、doPost等方法
	- 如果不写service方法，则会通过对应的提交方式去执行doGet或者doPost


# 六、Servlet对象的生命周期

1. 所有的Servlet对象只能由Http服务器（Tomcat）来负责创建
2. Servlet对象被创建的时机
	1. 在绝大多数情况下，在Tomcat接收到一个用户对当前Servlet请求发送的时候，才会被创建 
	2. 在人工主观意愿调整的情况下，可以要求Tomcat在启动的时候就创建Servlet对象

```xml
<load-on-startup>1</load-on-startup>
```

3. 一个Servlet实体类在同一时间只能被创建出一个对象，（不能并存）单例！
4. 在Tomcat关闭时，负责将当前网页中的所有Servlet对象进行销毁


# 七、Servlet中的常用接口

## 1. HttpServletResponse接口

- 响应接口（对象）
- 作用：
	- 将doPost、doGet、service等方法处理后的结果通过响应的方式，返回给响应体
	- 设置响应头，响应类型（Content-Type=text/html;charset=utf-8）和字符集
	- 可以将响应地址写入响应头（跳转）

- 案例：在一个网页中输出Hello HttpServletResponse

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class AServlet extends HttpServlet {
    @Override
    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
//        super.service(req, res);
        res.setContentType("text/html;charset=utf-8");
        PrintWriter out = res.getWriter();
        out.write("<h2>write()</h2>");
        out.write(97);
        out.print(97);
        out.print("<h1>print one</h1>");
        out.print("<h2>print two</h2>");
    }
}
```

- 案例：通过响应对象跳转到其他的url - 地址栏发生变化

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class BServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.service(req, resp);
        String type = req.getParameter("type");
        String url01 = "/myweb/a.do?info=redirect";
        String url02 = "https://www.baidu.com";
        switch (type) {
            case "1":
                resp.sendRedirect(url01);
                break;
            case "2":
                resp.sendRedirect(url02);
                break;
        }
    }
}
```



