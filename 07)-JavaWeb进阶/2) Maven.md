
# 一、简介

>  Maven就是一个项目管理工具，将项目开发和管理的过程抽象成一个项目对象模型（POM）
>  POM - Project Object Model（项目对象模型）

## 1. Maven的作用

- 项目构建：提供标准的、跨平台的自动化项目构建方式
- 依赖管理：方便快捷的管理项目依赖的资源(.jar)，避免资源创建的版本冲突问题 
- 统一开发结构：提供标准、统一的项目结构

```
src
	main
		java
		resources
	test
		java
		resources
```

## 2. 相关概念

- 每一个项目就是一个对象
- 通过pom.xml文件，对于项目进行配置（每一个文件对应一个项目）
- 依赖管理
- 本地仓库
- 中央仓库
- 构建生命周期
- 插件


# 二、下载与安装

## 1. Maven下载

> 下载之后解压到指定目录即可，Maven需要JAVA_HOME环境变量支持，而且需要配置MAVEN_HOME

## 2. 目录结构

- bin：可执行文件目录
- boot：启动文件目录
- conf：配置文件目录
- lib：依赖库文件目录（.jar）

## 3. 配置

1. 配置`MAVEN_HOME`环境变量
2. 配置`Path`环境量：添加`%MAVEN_HOME\bin%`
3. 测试Maven `mvn -version`


# 三、Maven基出概念

## 1. 仓库

- 远程仓库
	- 中央仓库：用于存储资源，包含各种.jar包（开源包）
	- 私服仓库：共享仓库（非开源包）
- 本地仓库：自动从中央仓库获取

## 2. 坐标

> 用来查找资源的载体，可以把它理解成目录，根据坐标找资源

- Maven坐标的组成
	- groupID：当前Maven项目的隶属组
	- artfactID：当前Maven项目名
	- version：当前项目的版本号
	- packaging：当前项目的打包方式

https://mvnrepository.com/

## 3. 本地仓库配置

### a. 创建本地仓库

1. 在喜欢的地方创建一个仓库目录
2. 修改配置文件 `conf/settings.xml`

- 修改本地仓库

```xml
<!-- localRepository
| The path to the local repository maven will use to store artifacts.
|
| Default: ${user.home}/.m2/repository
<localRepository>/path/to/local/repo</localRepository>
-->
<localRepository>D:\DevEnv\repository</localRepository>
```

- 修改远程镜像仓库

https://developer.aliyun.com/

```xml
<!-- <mirror> -->
  <!-- <id>maven-default-http-blocker</id> -->
  <!-- <mirrorOf>external:http:*</mirrorOf> -->
  <!-- <name>Pseudo repository to mirror external repositories initially using HTTP.</name> -->
  <!-- <url>http://0.0.0.0/</url> -->
  <!-- <blocked>true</blocked> -->
<!-- </mirror> -->
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>central</mirrorOf>
  <name>阿里云公共仓库</name>
  <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```


# 四、Maven操作入门

## 1. 手动创建

1. 根据结构创建对应的目录
	- project-java
		- src
			- main
				- java
				- resources
			- test
				- java
				- resources
		- pom.xml

>main/java/com.maqf.demo/Demo.java

```java
package com.maqf.demo;

public class Demo {
	public String hello(String name) {
		String res = "Hello" + name;
		System.out.println(res);
		return res;
	}
}
```

> test/java/com.maqf.demo/DemoTest.java

```java
package com.maqf.demo;

import org.junit.Test;
import org.junit.Assert;

public class DemoTest {
	@Test
	public void testHello() {
		Demo d = new Demo();
		String ret = d.hello("Jack");
		Assert.assertEquals("HelloJack", ret);
	}
}
```

> pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project
	xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	
	<modelVersion>4.0.0</modelVersion>
	
	<groupId>com.maqf.demo</groupId>
	<artifactId>project-java</artifactId>
	<version>1.0</version>
	<packaging>jar</packaging>
	
	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
		</dependency>
	</dependencies>
</project>
```

- 相关命令

命令 | 说明
:- | :-
mvn compile | 编译项目
mvn clean | 清理项目
mvn test | 测试项目
mvn package | 打包项目
mvn install | 安装到本地仓库


## 2. 通过插件自动创建

- 创建工程语法

```bat
mvn archetype:generate 
-DgroupId=包名
-DartifactId=项目名 
-DarchetypeArtifactId=maven-archetype-quickstart 
-Dversion=0.0.1-snapshot 
-DinteractiveMode=false
```

- 创建Java工程

```bat
mvn archetype:generate -DgroupId=com.xuexi -DartifactId=web-project -DarchetypeArtifactId=maven-archetype-quickstart -Dversion=0.0.1-snapshot -DinteractiveMode=false
```

- 创建 JavaWeb工程

```bat
mvn archetype:generate -DgroupId=com.xuexi -DartifactId=web-project -DarchetypeArtifactId=maven-archetype-webapp -Dversion=0.0.1-snapshot -DinteractiveMode=false
```

## 3. IDEA中使用Maven 

- 添加Java工程
	1. 添加模块时选择Maven
- 添加JavaWeb工程
	1. 添加模块时选择JavaEE

> 注意：File -> Settings -> Build, Execution, Deployment -> Build Tools -> Maven 设置成自己安装的Maven及配置文件和repository。


# 五、依赖管理

## 1. 依赖配置

> 依赖当前项目所需要的jar包，一个项目可以设置多个依赖

```xml
<!-- 设置当前项目所需要的所有jar包 -->
<dependencies>
	<!-- 设置具体的依赖 -->
	<dependency>
		<!-- 依赖所属级 -->
		<groupId>org.junit.jupiter</groupId>
		<!-- 依赖所项目ID -->
		<artifactId>junit-jupiter-engine</artifactId>
		<!-- 依赖版本号 -->
		<version>${junit.version}</version>
	</dependency>
</dependencies>
```

## 2. 依赖传递

就近原则
- 路径就近：层级越近的优先级越高
- 声明就近：同级的越先声明就优先级越高
- 特殊覆盖：同一个依赖关系中，后设置的会覆盖之前的

## 3. 可选依赖

```xml
<dependency>
	<groupId>org.junit.jupiter</groupId>
	<artifactId>junit-jupiter-engine</artifactId>
	<version>${junit.version}</version>
	<!-- false表示不隐藏，true表示隐藏（不被别人依赖） -->
	<optional>true</optional>
</dependency>
```

## 4. 排除依赖

```xml
<dependency>
	<groupId>org.junit.jupiter</groupId>
	<artifactId>junit-jupiter-engine</artifactId>
	<version>${junit.version}</version>
	<!-- 不使用其他jar中的依赖 -->
	<exclusions>
		<exclusion>
			<groupId>xxx</groupId>
			<artifacId>xxx</artifacId>
		</exclusion>
	</exclusions>
</dependency>
```

## 5. 依赖范围

> 依赖的jar包默认情况下可以使用在任何位置，通过scope标签定义作用范围

```xml
<dependency>
	<groupId>org.junit.jupiter</groupId>
	<artifactId>junit-jupiter-engine</artifactId>
	<version>${junit.version}</version>
	<!-- 定义作用范围 -->
	<scope>test</scope>
</dependency>
```

- 作用范围
	- 主程序范围有效（main目录）
	- 测试程序范围有效（test目录）
	- 是否参与打包（package指令）

scope | main目录 | test目录 | 打包 | 例子
:- | :- | :- | :- | :-
compile（默认）| 有效 | 有效 | 有效 | log4j
test | | 有效 | | junit
provided | 有效 | 有效 | | servlet-api
runtime | | | 有效 | jdbc

## 6. 依赖范转传递

> 带有依赖范围的资源在进行递时，作用范围参考如下
> 横向为直接依赖，纵向为直接依赖的直接依赖

&nbsp; | compile | test | provided | runtime
:- | :- | :- | :- | :-
complie | compile | test | provided | runtime
test | 无 | 无 | 无 | 无
provided | 无 | 无 | 无 | 无
runtime | runtime | test | provided | runtime

