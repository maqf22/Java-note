
> Ajax是与服务器交换数据并更新部分网页的技术，在不重新加载整个页的面的情况下。
> Ajax是可以在页面无刷新的新情况下，与服务器的数据完成交互之后更新当前页面的部分内容

# 一、原生AJAX四部曲

1. 在浏览器对象中创建一个异步请求对象

```js
xmlhttp = new XMLHttpRequest();
```

2. 为异步请求对象绑定一个“工作状态监听器”

```js
xmlhttp.onreadystatechange = function() {
	if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
		document.getElementById("myDiv").innerHTML = xmlhttp.responseText;
	}
}
```

3. 初始化请求（设置请求方式，参数，是否同步）

```js
xmlhttp.open("GET", "text1.txt", true);
```

4. 命令异步请求对象代替浏览器向服务器端发送请求

```js
xmlhttp.send();
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>register</title>
</head>
<body>
<h3>register</h3>
<hr>
<form action="#">
    <table>
        <tr>
            <td>username:</td>
            <td>
                <input type="text" name="username" placeholder="username" onblur="doCheck(this.value)">
                <span id="sid"></span>
            </td>
        </tr>
        <tr>
            <td>password:</td>
            <td><input type="password" name="password" placeholder="password"></td>
        </tr>
        <tr>
            <td>sure psd:</td>
            <td><input type="password" name="psd" placeholder="sure password"></td>
        </tr>
    </table>
    <input type="submit" value="ok"/>
</form>
</body>
<script>
    function doCheck(username) {
        if (username === "") return;
        let xmlHttpRequest = new XMLHttpRequest();
        xmlHttpRequest.onreadystatechange = () => {
            if (xmlHttpRequest.readyState === 4 && xmlHttpRequest.status === 200) {
                let info = xmlHttpRequest.responseText;
                document.getElementById("sid").innerHTML = JSON.parse(info).data;
            }
        };
        // xmlHttpRequest.open("GET", `/myweb/doCheck.do?username=${username}`, true);
        // xmlHttpRequest.send();
        xmlHttpRequest.open("POST", "/myweb/doCheck.do", true);
        xmlHttpRequest.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
        xmlHttpRequest.send(`username=${username}`);
    }
</script>
</html>
```

```java
package com.ajax.controller;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.ArrayList;

public class DoCheckServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
        String username = req.getParameter("username");
        // 模似数据库对比“select count(*) from `stu` where `username` = ?”
        boolean b = userNameExists(username);
        PrintWriter out = resp.getWriter();
        if (b) {
            out.print("{ \"code\": 0, \"data\": \"username is exists\" }");
        } else {
            out.print("{ \"code\": 0, \"data\": \"username is okay\" }");
        }
    }

    private boolean userNameExists(String username) {
        ArrayList<String> nameList = new ArrayList<>();
        nameList.add("admin");
        return nameList.contains(username);
    }
}
```


# 二、AJAX - JQuery写法 

```js
function doCheck(username) {
	$.ajax({
		type: "POST",
		data: {
			username,
		},
		dataType: "JSON",
		url: "/myweb/doCheck.do",
		success(res) {
			$("#sid").html(res.data);
		},
		error() {
			alert("error")
		}
	});
}
```





