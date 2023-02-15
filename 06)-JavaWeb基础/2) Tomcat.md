
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

- 案例：通过请求的转发来实现跳转 - （请求对象实现）

```java
请求对象.getRequestDispatcher("站内转发地址").forward(当前的请求对象, 当前的响应对象);
```

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class CServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.service(req, resp);
        RequestDispatcher rd = req.getRequestDispatcher("/d.do");
        rd.forward(req, resp);
    }
}
```

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class DServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.service(req, resp);
        String info = req.getParameter("info");
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out = resp.getWriter();
        out.print("<h2>" + info + "</h2>");
    }
}
```

- 重定向 与 请求转发 之间的关系
	- 前提：通常同某一次请求可能会需要多个Servlet来协同完成某一项工作，那么这个工作是让用户一个一个的去发送请求。
	- 重定向：（请求方式：GET）
		- 响应对象.sendRedirect("url地址")，注意：通过重定向方式的跳转，地址栏会随之发生变化
		- 可以跳转的范围
			- 站内URI（/网站名/静太(动态)资源文件名）
			- 站外URL（https:// 完整的访问域名）
		- 请求转发：（请求方式：根据原始的请求方式来决定）
			- 只能在站内进行跳转（不可转发到站外）
			- 不适用请求转发的几种场景（以下三种场景适合重定向最实用的三种场景）
				- 添加之后的查询功能
				- 删除之后的查询功能
				- 修改之后的查询功能

```java
RequestDispatcher report = 请求对象名.getRequestDispatcher("uri地址");
report.forward(当前的请求对象, 当前的响应对象)；
```


## 2. HttpServletRequest - 请求对象

- 获取请求行中的一些内容

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class EServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doGet(req, resp);
        String param = req.getParameter("param");
        String URI = req.getRequestURI();
        StringBuffer URL = req.getRequestURL();
        String method = req.getMethod();
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out = resp.getWriter();
        out.print("<h2>" + param + "</h2><hr/>");
        out.print("<h2>" + URI + "</h2><hr/>");
        out.print("<h2>" + URL + "</h2><hr/>");
        out.print("<h2>" + method + "</h2><hr/>");
    }
}
```

- 获取请求包中的一些内容

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;

public class FServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.service(req, resp);
        if (req.getMethod().equals("POST"))
            req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
        Enumeration<String> paramKeys = req.getParameterNames();
        PrintWriter out = resp.getWriter();
        while (paramKeys.hasMoreElements()) {
            String key = paramKeys.nextElement();
            String value = req.getParameter(key);
            String res = "key: " + key + "<br/>" + "value: " + value + "<br/>";
            out.print(res);
        }
    }
}
```

- 获取多个值

```html
<form action="/myweb/g.do" method="post">  
    code <input type="checkbox" name="hobby" value="0"/>  
    print <input type="checkbox" name="hobby" value="1"/>  
    read <input type="checkbox" name="hobby" value="2"/>  
    <button>submit</button>  
</form>
```

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class GServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doPost(req, resp);
        String[] values = req.getParameterValues("hobby");
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out = resp.getWriter();
        for (String value : values) {
            out.print(value + "<br/>");
        }
    }
}
```

> request 请求对象的生命周期
> - 创建时机：在Tomcat接收到请求，调用doGet/doPost/service等对应响应方法的时候被创建，然后这个响应对象会被自动传递给对应的响应方法
> - 销毁时机：当响应方法调用结束之后，意味着本次的请求已经处理完毕，在Tomcat负责推送响应包之前，销毁掉请求对象

- HTTP服务器状态码：
	- 100：通知浏览器，服端本次返回的资源文件不是一个完整的资源文件，需浏览器对服务器继续发送请求来获取当前请求所需的其他文件或相关联的资源文件
	- 200：正常
	- 302：通知浏览器不需要读取响应体中的内容，此时在响应头存在一个Location属性，要求浏览器根据Location属性的地址向服务器发起resp.sendRedirect(url)请求。（重定向）
	- 404：没有找到目录资源文件
	- 405：当前Servlet无法对当前的请求方式进行处理（请求方式有问题）
	- 500：本次要访问的Servlet进行处理时，产生了Java异常（服务端错误）


## 3. ServletContext接口

- 全局作用域对象，通过该对象创建的数据可以在多个Servlet中进行共享
- 生命周期
	- Tomcat启动的时候，自动创建一个全局作用域对象
	- 一个网站项目有且只有一个全局作用域对象
	- Tomcat关闭时自动销毁

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class ServletA extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doGet(req, resp);
        ServletContext context = req.getServletContext();
        context.setAttribute("Teacher", "Lily");
        context.setAttribute("Student", "Jack");
    }
}
```

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class ServletB extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doGet(req, resp);
        ServletContext context = req.getServletContext();
        String teacher = null;
        String student = null;
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out = resp.getWriter();
        try {
            teacher = context.getAttribute("Teacher").toString();
            student = context.getAttribute("Student").toString();
            out.print("<h3>" + teacher + "</h3>");
            out.print("<h3>" + student + "</h3>");
        } catch (Exception e) {
//            throw new RuntimeException(e);
            out.print("<h3>NullPointerException</h3>");
        }
    }
}
```


## 4. Cookie工具类

- 一个可以实现数据共享的工具类，cookie是存储在浏览器端的数据共享介质

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class ServletC extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doGet(req, resp);
        Cookie userNameCookie = new Cookie("username", "admin");
        Cookie passwordCookie = new Cookie("password", "123456");
        userNameCookie.setMaxAge(60);
        passwordCookie.setMaxAge(30);
        resp.addCookie(userNameCookie);
        resp.addCookie(passwordCookie);
    }
}
```

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class ServletD extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doGet(req, resp);
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out = resp.getWriter();
        Cookie[] cookies = req.getCookies();
        if (null == cookies) {
            out.print("<p>no cookie</p>");
            return;
        }
        for (Cookie cookie : cookies) {
            String name = cookie.getName();
            String value = cookie.getValue();
            out.print("<p>" + name + " => " + value + "</p>");
        }
    }
}
```


## 5. HttpSession接口

- 一个可以实现数据共享的工具类，session是存储在服务器端的数据共享介质

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class ServletE extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doGet(req, resp);
        HttpSession session = req.getSession();
        session.setAttribute("username", "admin");
        session.setAttribute("password", "123456");
    }
}
```

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class ServletF extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doGet(req, resp);
        HttpSession session = req.getSession();
        String username = null;
        String password = null;
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out = resp.getWriter();
        try {
            username = session.getAttribute("username").toString();
            password = session.getAttribute("password").toString();
            out.print("<p>" + username + " => " + password + "</p>");
        } catch (Exception e) {
//            throw new RuntimeException(e);
            out.print("<p>no session</p>");
        }
    }
}
```

- Cookie和Session对比

&nbsp;| 存储位置 | 存储量 | 数据类型 | 应用场景
 :- | :- | :- | :- | :-
 Cookie | 客户端 | 一个键值对 | 只能String | 记住用户名
 Session | 服务端 | 任意数量的键值对 | 任意数据类型 | 较私密的数据

- Session的生命周期
	- 默认情况下：Session的有效时长（最大闲置时间）是30分钟
	- 手动设置：项目的核心配置文件web.xml，设置最大闲置时长为10分钟

```xml
<session-config>
	<session-timeout>10</session-timeout>
</session-config>
```


## 6. Listener监听接口

- 用来监听Servlet规范中作用域对象盛行周期以及共享数据的变化
- 没有现成的方法，需要自己的实现接口
- 监听器实现类的开发步骤
	1. 根据我们要监听的内容来选择一个对应的接口类来进行实现
	2. 重写该接口中的监听处理函数
	3. 项目核心配置文件web.xml中进行注册

```java
package com.servlet.demo;

import javax.servlet.*;
import javax.servlet.http.*;

public class Listener implements ServletContextListener, HttpSessionListener, HttpSessionAttributeListener {

    public Listener() {
    }

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        /* This method is called when the servlet context is initialized(when the Web application is deployed). */
        System.out.println("ServletContext对象被创建了！");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        /* This method is called when the servlet Context is undeployed or Application Server shuts down. */
        System.out.println("ServletContext对象被销毁了！");
    }

    @Override
    public void sessionCreated(HttpSessionEvent se) {
        /* Session is created. */
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        /* Session is destroyed. */
    }

    @Override
    public void attributeAdded(HttpSessionBindingEvent sbe) {
        /* This method is called when an attribute is added to a session. */
    }

    @Override
    public void attributeRemoved(HttpSessionBindingEvent sbe) {
        /* This method is called when an attribute is removed from a session. */
    }

    @Override
    public void attributeReplaced(HttpSessionBindingEvent sbe) {
        /* This method is called when an attribute is replaced in a session. */
    }
}
```

- 应用场景（数据库的连接池connect pool）

> 在我们使用Java对数据库进行操作的时候，需要创建数据库的连接，不适用的时候还需要对数据库的连接进行关闭和资源释放。那么其实创建连接和释放资源的过程是非常影响我们对数据库操作的效率的。通常响应时长都很久。假设一个次建立连接和释放连接资源的总时间为30ms，那么我们做10次查询，就要耗费300ms。很多时候我们的操作非常频繁。那么就需要用数据库连接池相关的方式来解决。
> ServletContext监听是从Tomcat启动开始，到关闭结束。所以可以在Tomcat启动时候就创建好很多的数据库连接，等着Servlet来使用。使用结束之后也进行关闭操作，在还给数据连接池，等待其他servlet来进行使用


## 7. Filter过滤接口


















