
> Extensible Markup Language 可扩展标记语言

- 可扩展：标签都是自定义的。`<user> <student>`
- 功能：存储数据
	- 配置文件
	- 在网络中传输
- XML / HTML / XHTML
	- xml标签都是自定义的，html标签预定义
	- xml的语法严格，html语法松散
	- xml是存储数据的，html是展示数据


# 一、语法

## 1. 基本语法

1. xml文档的后缀名.xml
2. xml第一行必须定义为文档声明
3. xml文档中有且仅有一个根标签 
4. 属性值必须使用引号引起来（单双均可）
5. 标签必须闭合
6. xml标签名称区分大小写

```xml
<?xml version="1.0" encoding="utf-8" ?>

<students>
	<student id="1">
		<name>Jack</name>
		<age>19</age>
		<gender>male</gender>
	</student>
</students>
```

## 2. 组成部分

- 文档声明
	- 格式：`<?xml 属性列表 ?>`
		- 属性列表：
			- version：版本号，必须的属性
			- encoding：编码方式。告知解析引擎当前文档使用的字符集，默认值：ISO-8859-1
			- standalone：是否独立
				- 取值：
					- yes：不依赖其他文件
					- no：依赖其他文件
- 指令：结合css的`<?xml-stylesheet type="text/css" href="a.css" ?>`
- 标签：标签名称自定义的
	- 规则：
		- 名称可以包含字母、数字以及其他的字符
		- 名称不能以数字或者标点符号开始
		- 名称不能以字母xml（或者XML、Xml等）开始
		- 名称不能包含空格
- 属性：
	- id属性值唯一
- 文本：
	- CDATA区：在该区域中的数据会实原样展示
	- 格式：`<![CDATA[ 数据 ]]>`


# 二、约束

> 规定xml文档的书写规则

- 使用框架时：
	1. 能够在xml中引入约束文档
	2. 能够简单的读懂约束文档
- 分类：
	1. DTD：一种简单的约束技术
	2. Schema：一种复杂的约束技术

DTD

```dtd
<!ELEMENT students (student+)>
<!ELEMENT student (name, age, sex)>
<!ELEMENT name (#PCDATA)>
<!ELEMENT age (#PCDATA)>
<!ELEMENT sex (#PCDATA)>
<!ATTLIST student id #REQUIRED>
```

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE students SYSTEM "students.dtd">
<students>
	<student id="1">
		<name>Jack</name>
		<age>19</age>
		<gender>male</gender>
	</student>
</students>
```

Schema

```xsd
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
targetNamespace="http://www.w3school.com.cn"
xmlns="http://www.w3school.com.cn"
elementFormDefault="qualified">

<xs:element name="note">
    <xs:complexType>
      <xs:sequence>
	<xs:element name="to" type="xs:string"/>
	<xs:element name="from" type="xs:string"/>
	<xs:element name="heading" type="xs:string"/>
	<xs:element name="body" type="xs:string"/>
      </xs:sequence>
    </xs:complexType>
</xs:element>

</xs:schema>
```

```xml
<?xml version="1.0"?>
<note
xmlns="http://www.w3school.com.cn"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.w3school.com.cn note.xsd">

<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```


# 三、解析

> 操作xml文档，将文档中的数据读取到内存中

- 操作xml文档
	- 解析（读取）：将文档中的数据读取到内存中
	- 写入：将内存中的数据保存到xml文档中。持久化的存储
- 解析xml的方式：
	1. DOM：将标记语言文档一次性加载进内存，在内存中形成一颗dom树
		- 优点：操作方便，可以对文档进行CRUD的所有操作
		- 缺点：占内存
	2. SAX：逐行读取，基于事件驱动的。
		- 优点：不占内存
		- 缺点：只能读取，不能增删改


## 1. xml常见的解析器

1. JAXP：sum公司提供的解析器，支持dom和sax两种思想
2. DOM4J：一款非常优秀的解析器
3. PULL：Android操作系统内置的解析器，sax方式。
4. Jsoup：Jsoup是一款Java的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于JQuery的操作方法来取出和操作数据


## 2. Jsoup的使用

- 操作步骤
	1. 导入jar包
	2. 获取Document对象
	3. 获取对应的标签Element对象
	4. 获取数据

```java
package com.xml.jsoup;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.io.File;
import java.io.IOException;

public class Demo01 {
    public static void main(String[] args) throws IOException {
        String path = "xml.xml";
        // String path = Demo01.class.getClassLoader().getResource("./xml.xml").getPath();
        Document document = Jsoup.parse(new File(path), "utf-8");
        Elements name = document.getElementsByTag("name");
        for (Element element : name) {
            System.out.println(element.text());
        }
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8" ?>

<students>
	<student id="1">
		<name>Jack</name>
		<age>19</age>
		<gender>male</gender>
	</student>
	<student id="2">
		<name>Rose</name>
		<age>18</age>
		<gender>female</gender>
	</student>
</students>
```

### a. 常用方法

方法名 | 说明
:- | :-
public static Document parse(File in, String charseName) | 解析XML或者HTML文件
public static Document parse(String htmlCode) | 解析XML或HTML代码串
public static Document parse(URL url, int timeoutMillis) | 网络获取指定的html或xml的对象
public Element getElementById(String id) | 根据id获取节点对象
public Element getElementByTag(String tagName) | 根据标签名获取节点的结合
public Elements getElementByAttribute(String attr) | 根据标签包含的属性获取节点的结合的集合
public Element getElementByAttributeValue(String attr, String value) | 根据标签名属性对应的值获取节点的结全的集合
public String attr(String attr) | 根据属性名获取属性的值
public String html() | 获取标签体内的所有内容
public String text() | 获取文本内容

```java
package com.xml.jsoup;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;

public class Demo02 {
    public static void main(String[] args) {
        String xml = "<?xml version=\"1.0\" encoding=\"utf-8\" ?>\n" +
                "\n" +
                "<students>\n" +
                "\t<student id=\"1\">\n" +
                "\t\t<name>Jack</name>\n" +
                "\t\t<age>19</age>\n" +
                "\t\t<gender>male</gender>\n" +
                "\t</student>\n" +
                "\t<student id=\"2\">\n" +
                "\t\t<name>Rose</name>\n" +
                "\t\t<age>18</age>\n" +
                "\t\t<gender>female</gender>\n" +
                "\t</student>\n" +
                "</students>";
        Document document = Jsoup.parse(xml);
        System.out.println(document);
    }
}
```

```java
package com.xml.jsoup;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;

import java.io.IOException;
import java.net.URL;

public class Demo03 {
    public static void main(String[] args) throws IOException {
        String url_txt = "https://www.baidu.com";
        URL url = new URL(url_txt);
        Document document = Jsoup.parse(url, 5000);
        System.out.println(document);
    }
}
```

```java
package com.xml.jsoup;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.select.Elements;

import java.io.File;
import java.io.IOException;

public class Demo04 {
    public static void main(String[] args) throws IOException {
        String path = "xml.xml";
        Document document = Jsoup.parse(new File(path));

        Elements names = document.getElementsByTag("name");
        System.out.println(names);
        Elements ids = document.getElementsByAttribute("id");
        System.out.println(ids);
        Elements val = document.getElementsByAttributeValue("id", "1");
        System.out.println(val);
    }
}
```

```java
package com.xml.jsoup;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;

import java.io.File;
import java.io.IOException;

public class Demo05 {
    public static void main(String[] args) throws IOException {
        String path = "xml.xml";
        Document document = Jsoup.parse(new File(path));
        Element elementById = document.getElementById("1");
        System.out.println(elementById);
        String text = elementById.text();
        System.out.println(text);
        String html = elementById.html();
        System.out.println(html);
        String id = elementById.attr("id");
        System.out.println(id);
    }
}
```

### b. 快捷查询方式

- 使用的方法：Element select(String cssQuery) --（选择器）
- 语法：参考Selector类中定义的语法

```java
package com.xml.jsoup;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.select.Elements;

import java.io.File;
import java.io.IOException;

public class Demo06 {
    public static void main(String[] args) throws IOException {
        String path = "xml.xml";
        Document doc = Jsoup.parse(new File(path));
        
        Elements name = doc.select("name");
        System.out.println(name);
        Elements id = doc.select("#1");
        System.out.println(id);
        Elements age = doc.select("student[id='2'] > age");
        System.out.println(age);
    }
}
```

### c. XPath

> XPath即为XML路径语言，它是一种用来确定XML（标准通用标记语言的子集）文档中某部分位置的语言

- 使用Jsoup的Xpath需要额外导入jar包
- 查询w3cschool参考手册，使用xpath的语法完成查询






