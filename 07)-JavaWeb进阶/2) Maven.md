
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




