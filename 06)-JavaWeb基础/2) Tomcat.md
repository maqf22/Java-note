
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




