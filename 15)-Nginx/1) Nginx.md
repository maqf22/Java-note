
# 一、简介

## 1. Nginx介绍

> https://xuexb.github.io/learn-nginx/guide/

Nginx（发音同engine x）是一个异步框架的 Web 服务器，也可以用作反向代理，负载平衡器 和 HTTP 缓存。该软件由 [Igor Sysoev](https://zh.wikipedia.org/wiki/%E4%BC%8A%E6%88%88%E7%88%BE%C2%B7%E8%B3%BD%E7%B4%A2%E8%80%B6%E5%A4%AB) 创建，并于2004年首次公开发布。同名公司成立于2011年，以提供支持。Nginx 是一款免费的开源软件，根据类 BSD 许可证的条款发布。一大部分Web服务器使用 Nginx ，通常作为负载均衡器。

## 2. 正向代理和反向代理概念

> https://cloud.tencent.com/developer/article/1418457

### 1）正向代理

正向代理（forward proxy）：是一个位于客户端和目标服务器之间的服务器(代理服务器)，为了从目标服务器取得内容，客户端向代理服务器发送一个请求并指定目标，然后代理服务器向目标服务器转交请求并将获得的内容返回给客户端。

这种代理其实在生活中是比较常见的，比如访问外国网站技术，其用到的就是代理技术。

有时候，用户想要访问某国外网站，该网站无法在国内直接访问，但是我们可以访问到一个代理服务器，这个代理服务器可以访问到这个国外网站。这样呢，用户对该国外网站的访问就需要通过代理服务器来转发请求，并且该代理服务器也会将请求的响应再返回给用户。这个上网的过程就是用到了正向代理。

![[Pasted image 20230615153205.png]]

这个过程其实和租房子很像。

租房子的时候，一般情况下，我们很难联系到房东，因为有些房东为了图方便，只把自己的房屋信息和钥匙交给中介了。而房客想要租房子，只能通过中介才能联系到房东。而对于房东来说，他可能根本不知道真正要租他的房子的人是谁，他只知道是中介在联系他。

这里面一共有三个角色，租客（用户）、中介（代理服务器）和房东（国外网站，目标服务器）。引入中介（代理服务器）的原因是用户无法联系上房东（用户无法访问国外网站）。

**所以，正向代理，其实是"代理服务器"代理了"客户端"，去和"目标服务器"进行交互。**

通过正向代理服务器访问目标服务器，目标服务器是不知道真正的客户端是谁的，甚至不知道访问自己的是一个代理（有时候中介也直接冒充租客）。


#### **正向代理的用途**

**突破访问限制**

通过代理服务器，可以突破自身IP访问限制，访问国外网站，教育网等。

即，租客可以通过中介，来解决无法联系上房东的问题。

**提高访问速度**

通常代理服务器都设置一个较大的硬盘缓冲区，会将部分请求的响应保存到缓冲区中，当其他用户再访问相同的信息时， 则直接由缓冲区中取出信息，传给用户，以提高访问速度。

即，中介手里留存了很多房源信息和钥匙，可以直接带租客去看房。

**隐藏客户端真实IP**

上网者也可以通过这种方法隐藏自己的IP，免受攻击。

即，房东并不知道租客的真实身份。PS：但是中介知道了，可能骚扰更多….


### 2）反向代理

反向代理（reverse proxy）：是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

我们在租房子的过程中，除了有些房源需要通过中介以外，还有一些是可以直接通过房东来租的。用户直接找到房东租房的这种情况就是我们不使用代理直接访问国内的网站的情况。

还有一种情况，就是我们以为我们接触的是房东，其实有时候也有可能并非房主本人，有可能是他的亲戚、朋友，甚至是二房东。但是我们并不知道和我们沟通的并不是真正的房东。这种帮助真正的房主租房的二房东其实就是反向代理服务器。这个过程就是反向代理。

对于常用的场景，就是我们在Web开发中用到的[负载均衡](https://cloud.tencent.com/product/clb?from=20065&from_column=20065)服务器（二房东），客户端（租客）发送请求到负载均衡服务器（二房东）上，负载均衡服务器（二房东）再把请求转发给一台真正的服务器（房东）来执行，再把执行结果返回给客户端（租客）。

![[Pasted image 20230615153403.png]]

**所以，反向代理，其实是"代理服务器"代理了"目标服务器"，去和"客户端"进行交互。**

通过反向代理服务器访问目标服务器时，客户端是不知道真正的目标服务器是谁的，甚至不知道自己访问的是一个代理。


#### **反向代理的用途**

**隐藏服务器真实IP**

使用反向代理，可以对客户端隐藏服务器的IP地址。

即，租客并不房东知道的真实身份。

**负载均衡**

反向代理服务器可以做负载均衡，根据所有真实服务器的负载情况，将客户端请求分发到不同的真实服务器上。

即，二房东发现房主本人很忙，于是找到房主的妻子帮忙处理租房事宜。

**提高访问速度**

反向代理服务器可以对于静态内容及短时间内有大量访问请求的动态内容提供缓存服务，提高访问速度。

即，二房东同样有房屋信息和钥匙。

**提供安全保障**

反向代理服务器可以作为应用层防火墙，为网站提供对基于Web的攻击行为（例如DoS/DDoS）的防护，更容易排查恶意软件等。还可以为后端服务器统一提供加密和SSL加速（如SSL终端代理），提供HTTP访问认证等。

即，二房东可以有效的保护房东的安全。


### 3) 正向代理和反向代理的区别

虽然正向代理服务器和反向代理服务器所处的位置都是客户端和真实服务器之间，所做的事情也都是把客户端的请求转发给服务器，再把服务器的响应转发给客户端，但是二者之间还是有一定的差异的。

1、**正向代理其实是客户端的代理**，帮助客户端访问其无法访问的服务器资源。**反向代理则是服务器的代理**，帮助服务器做负载均衡，安全防护等。

2、**正向代理一般是客户端架设的**，比如在自己的机器上安装一个代理软件。而**反向代理一般是服务器架设的**，比如在自己的机器集群中部署一个反向代理服务器。

3、**正向代理中，服务器不知道真正的客户端到底是谁**，以为访问自己的就是真实的客户端。而在**反向代理中，客户端不知道真正的服务器是谁**，以为自己访问的就是真实的服务器。

4、正向代理和反向代理的作用和目的不同。**正向代理主要是用来解决访问限制问题。而反向代理则是提供负载均衡、安全防护等作用。二者均能提高访问速度。**


# 二、Nginx环境搭建

## 1. 下载

免费开源版的官方网站：https://nginx.org

Nginx有Windows版本和Liunx版本，但更推荐在Liunx下使用Nginx

## 2. 安装

### 1) 安装前的准备

Nginx的安装需要确定Linux安装相关的几个库，否则配置和编译会出现错误，具体的检查和安装过程为：

1. gcc编译器是否安装
	检查是否安装：`yum list installed | grep gcc`
	执行安装：`yum install gcc -y`

2. openssl库是否安装
	检查是否安装：`yum list installed | grep openssl`
	执行安装：`yum install openssl openssl-devel -y`

3. pcre库是否安装
	检查是否安装：`yum list installed | grep pcre`
	执行安装：`yum install pcre pcre-devel -y`

4. zlib库是否安装
	检查是否安装：`yum list installed | grep zlib`
	执行安装：`yum install zlib zlib-devel -y`

5. 一次性安装，执行如下命令
	`yum install gcc openssl openssl-devel pcre pcre-devel zlib zlib-devel -y`


### 2) 正式安装

> https://xuexb.github.io/learn-nginx/guide/linux-install.html#%E4%B8%8B%E8%BD%BD%E5%AE%89%E8%A3%85%E5%8C%85%E5%B9%B6%E8%A7%A3%E5%8E%8B

1. 解压下载下来的nginx文件，执行命令：`tar -zxvf nginx-1.14.2.tar.gz`
2. 切换至解压后的nginx主目录，执行命令：`cd nginx-1.14.2`
3. 在nginx主目录nginx-1.14.2下执行命令：`./configure --prefix=/usr/local/nginx`（其中--prefix是指定nginx安装路径）注意：等号左右不要有空格
4. 执行命令进行安装：`make install`

> 安装成功后，可以切换到`/usr/local/nginx`目录下，查看内容


### 3) Ubuntu子系统中安装

1. 修改apt源，source.list文件中的内容（带魔法略过）

```
# 源自行搜索
```

2. 导入Nginx系统公钥

```
sudo wget http://nginx.org/keys/nginx_signing.key
sudo apt-key add nginx_signing.key
```

3. 更新apt源

`sudo apt-get update`

4. 安装Nginx

`sudo apt install nginx`

5. 测试启动&关闭&查看服务启动状态

```
# 启动Nginx服务
sudo service nginx start
# 关闭Nginx服务
sudo service nginx stop
# 查看Nginx服务状态
sudo service nginx status
```


## 3. 启动

### 1) 普通启动

- 通过服务命令启动`sudo service nginx start`
- 直接通过Nginx可执行文件启动`sudo nginx`

### 2) 通过配置文件启动

```
# 直接通过nginx命令启动，同时添加 -c 参数，指定配置文件（可以加-t来检查配置文件是否有错误）
sudo nginx -c /etc/nginx/nginx.conf
```

### 3) 检查Nginx是否启动

- 通过查看进程：`ps aux | grep nginx`
- nginx体系结构由master进程和其worker进程组成
- master进程读取配置文件，并维护worker进程，而worker进程则对请求进行实际处理
- nginx启动后，安装目录下会出现一此_tmp结尾的文件，这些是临时文件，不用管
- 在浏览器中输入localhost访问nginx服务器

### 4) 关闭

#### a. 优雅关闭Nginx

- 找出nginx的进程号：ps -ef | grep nginx
- 行命令：kill -QUIT 主pid

> **注意**
> 其中pid是主进程号的pid(master process)，其他为子进程pid(worker pr-ess)
> 这种关闭方式会处理完请求后再关闭，所以称之为优雅的关闭

#### b. 快速关闭Nginx

- 找出nginx的进程号：`ps aux | grep nginx`
- kill -TERM 主pid

> 注意
> 其中pid是主进程号的pid(master process)，其他为子进程pid(worker process)
> 这种关闭方式不管请求是否处理完成，直接关闭

#### c. 重启Nginx

`nginx -s reload`

### 5) 配置检查

当修改Nginx配置文件后，可以使用Nginx命令进行配置文件语法检查，用检查Nginx配置文件是否正确

`sudo nginx -c /etc/nginx/nginx.conf -t`

### 6) 其它

- Liunx上查看nginx版本：`/usr/local/nginx/sbin/nginx -V`
	- -v（小写的v）显示nginx的版本
	- -V（大写的V）显示nginx的版本、编译器版本和配置参数


# 三、Nginx配置文件说明及Nginx主要应用

> [!TIP]
> 不同版本存在一定的差异，具体版本请查看网方文档  https://nginx.org

## 1. Nginx的核心配置文件

> 这个文件位于Nginx的安装目录`/etc/nginx/`目录下，`nginx.conf`

*不同版本nginx可能存在差异*
![[Pasted image 20230619151411.png]]

### 1) 基本配置

```conf
# 配置worker进程运行用户 nobody也是一个Linux用户，一般用于启动程序，没有密码
user www-data;

# 配置工作进程数目，根据硬件调整，通常等于CPU数量或者2倍CPU数量
worker_processes auto;

# 配置全局错误日志及类型，[debug | info | notice | warn | error | crit]，默认是error
error_log logs/error.log
# error_log logs/error.log notice
# error_log logs/error.log info

# 配置进程pid文件
pid /run/nginx.pid;
```

### 2) events配置

```conf
# 配置工作模式和连接数
events {
	# 配置每个worker进程连接数上限，nginx支持的总连接数就等于worker_processes乘worker_connections
	worker_connections 768;
	# multi_accept on;
}
```

### 3) http配置

1. 基本配置

```conf
# 配置http服务器，利用它的反向代理功能提供负载均衡支持
http {

	##
	# Basic Settings
	##

	# 开启高效文件转输模式
	sendfile on;
	# 防止网络阻塞
	tcp_nopush on;
	tcp_nodelay on;
	# 长连接超时时间，单位为秒
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	# 配置nginx支持哪些多媒体类型，可以在conf/mime.types查看支持哪些多媒体类型
	include /etc/nginx/mime.types;
	# 默认文件类型 流类型，可以理解为支持任意类型
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	# 配置access.log日志及存放路径
	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	# 开启gzip压缩输出：
	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}
```

2. server配置虚拟主机

```conf
server {
	# 配置监听端口
	listen 80 default_server;
	listen [::]:80 default_server;

	# SSL configuration
	#
	# listen 443 ssl default_server;
	# listen [::]:443 ssl default_server;
	#
	# Note: You should disable gzip for SSL traffic.
	# See: https://bugs.debian.org/773332
	#
	# Read up on ssl_ciphers to ensure a secure configuration.
	# See: https://bugs.debian.org/765782
	#
	# Self signed certs generated by the ssl-cert package
	# Don't use them in a production server!
	#
	# include snippets/snakeoil.conf;

	# root是配置服务器的默认网站根目录位置，默认为nginx安装主目录下的html目录
	root /var/www/html;

	# Add index.php to the list if you are using PHP
	index index.html index.htm index.nginx-debian.html;

	# 配置服务名
	server_name _;

	# 默认的匹配斜杠/的请求，当访问路径中有斜杠/，会被该location匹配到并进行处理
	location / {
			# First attempt to serve request as file, then
			# as directory, then fall back to displaying a 404.
			try_files $uri $uri/ =404;
	}

	# pass PHP scripts to FastCGI server
	#
	#location ~ \.php$ {
	#       include snippets/fastcgi-php.conf;
	#
	#       # With php-fpm (or other unix sockets):
	#       fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
	#       # With php-cgi (or other tcp sockets):
	#       fastcgi_pass 127.0.0.1:9000;
	#}

	# deny access to .htaccess files, if Apache's document root
	# concurs with nginx's one
	#
	#location ~ /\.ht {
	#       deny all;
	#}
}
```

## 2. Nginx主要应用

- 静态网站部署
- 负载均衡
- 静态代理
- 动静分离
- 虚拟主机


# 四、静态网站部署

> Nginx是一个Http的web服务器，可以将服务器上的静态文件（html，图片等）通过http协议返回给浏览器客户端

1. 在Liunx家目录中创建myweb目录，用于部署静态网站，创建myNginxConfigure目录，用于存储类中文件
	`mkdir ~/myWeb ~/myNginxConfigure`
2. 准备一个网站项目或者网页，用于测试
3. 将准备好的网站打包存放到`~/myweb`目录中
4. 修改`nginx.conf`配置文件
	- 拷贝两个配置文件到当前用户家目录中创建`myNginxConfigure`配置文件存放目录
		`sudo cp /etc/nginx/conf.d/default.conf /etc/nginx/nginx.conf ~/myNginxConfigure`
	- 进入`myNginxConfigure`目录，并使用Windows资源管理器打开目录，方便下一步编辑配置文件
		`cd ~/myNginxConfigure`
		`explorer.exe`
5. 修改`8090-server.conf`配置文件
```
listen 8090;

location /www {
	alias /home/maqf/www
	index index.html
}
```
6. 修改nginx.conf配置文件
```
include /home/maqf/myNginxConfigure/8090-server.conf;
```
7. 重启Nginx服务器
	`nginx -s reload`
8. 在浏览器中输入`localhost:8090/www`进行测试


# 五、负载均衡

## 1. 概述

负载均衡（Load Balancing）是一种计算机网络技术， 用来在多个计算机（计算机集群）、网络连接、CPU、磁盘驱动器或其他资源中分配负载， 以达到最佳化资源使用、最大化吞吐率、最小化响应时间、同时避免过载的目的。  
使用带有负载均衡的多个服务器组件，取代单一的组件，可以通过冗余提高可靠性。  
负载均衡服务通常是由专用软体和硬件来完成。

## 2. 负载均衡实现方式

### 1) 硬件负载均衡

- 比如F5、深信服、Array等
	- 优点是有厂商专业的技术服务团队提供支持，性能稳定
	- 缺点是费用昂贵，对于规模较小的网络应用成本太高

### 2) 软件负载均衡

比如Nginx、LVS、HAProxy等
- 优点是免费开源，成本低谦

### 3) Nginx负载均衡

> Nginx通过在`nginx.conf`文件进行配置即可实现负载均衡

原理图
![[Pasted image 20230620112541.png]]

#### 实现步骤

 ##### a. 在Liunx中安装Tomcat

1. 命令行输入`sudo apt install openjdk-8-jdk`安装JDK

2. 配置环境变量`vi /etc/profile`
```
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=.:${JAVA_HOME}/bin:${PATH}
```

3. 激活配置文件使其永久生效
```
sudo source /etc/profile # 激活主配置文件（不用重启）
sudo source ~/.bashrc # 激活当前用户的bash配置文件
```

4. 命令行输入`sudo apt install tomcat9 tomcat9-docs tomcat9-examples tomcat9-admin`安装Tomcat9

5. 调整相关的配置
	1. 安装目录上创建`conf`和`logs`目录，`mkdir /usr/share/tomcat9/conf /usr/share/tomcat9/conf` 
	2. 复制配置文件到指定目录`cp /usr/share/tomcat9/etc/* /usr/share/tomcat9/conf/`
	3. 修改`/usr/share/tomcat9/conf/server.xml`配置文件
		1. 修改连接服务接端口&连接端口号
		```
		<Server port="-1" shutdown="SHUTDOWN">
		<Connector port="8099" 
			protocol="HTTP/1.1" 
			connectionTimeout="20000" 
			redirectPort="8443" />
		```
		2. 修改网站家目录位置
		```
		<Host name="localhost" 
			appBase="/var/lib/tomcat9/webapps" 
			unpackWARs="true" autoDeploy="true">
		```

 6. Tomcat相关命令
```
sudo sh /usr/share/tomcat9/bin/startup.sh
sudo sh /usr/share/tomcat9/bin/shutdown.sh
```

7. Windows相关命令
	`由于使用的是Windows中的子系统Ubuntu，所以查看端口号的占用情况需要使用Windows的命令行终端cmd`
- 查看端口号的占用情况，最后一列是进程ID，也就是pid
```
# 查看指定的端口号占用情况
netstat -aon | findstr "8090"
```
- 查看占用端口号的进程名
```
# 查看指定进程
tasklist | findsstr "16508"
```
- 结束进程，可通过Windows中的任务管理结束对应PID的进程


##### b. 源码包形式安装Nginx

> 之前通过apt安装的nginx对于ubuntu子系统的兼容有冲突

1. 安装gcc/g++的依赖库
```
sudo apt install build-essential
sudo apt install libtool
```

2. 安装pcre依赖库
```
sudo apt update
sudo apt install libpcre3 libpcre3-dev
```

3. 安装zlib依赖库
```
sudo apt install zliblg-dev
```

4. 安装ssl依赖库
```
sudo apt install openssl
```

5. 下载，解压并进入到源码包目录
```
tar axvf nginx-x.xx.x.tar.gz
cd ~nginx-x.xx.x
```

6. 配置、编译、执行安装
```
./configure
make
sudo make install
```

7. 通过可执行文件启动nginx
![[Pasted image 20230620170209.png]]

##### c. 在Linux服务器`/usr/share`目录下，拷贝两台新tomcat

```
sudo cp /usr/share/tomcat9 /usr/share/tomcat9-8081 -r
sudo cp /usr/share/tomcat9 /usr/share/tomcat9-8082 -r
```

##### d. 将这两台tomcat服务器webapps目录下没用的项目删掉

##### e. 分别修改两个拷贝出来的端口号为8081和8082，并将其他端口号升序加1即可，并制定webapps网站家目录

```
# 端口号修改
69     <Connector port="8081" protocol="HTTP/1.1"
70                connectionTimeout="20000"
71                redirectPort="8444" />

# appBase修还原
152       <Host name="localhost"  appBase="webapps"
153             unpackWARs="true" autoDeploy="true">

# 在<host>标签的最后一行添加
167       <Context path="" docBase="/usr/share/tomcat9-8081/webapps" debug="0" />

======
#8082同理
```

##### f. 分别在两个webapps目中添加index.html页面文件，内容做好区分，以便测试

```html
<!-- /usr/share/tomcat9-8081/webapps/index.html -->
<h1>Hello Tomcat port 8081</h1>

======
<!-- 8082同埋 -->
```

##### g. 分别启动两台tomcat，并查看服务启动状态

```
sudo sh /usr/share/tomcat9-8081/bin/startup.sh
sudo sh /usr/share/tomcat9-8082/bin/startup.sh
```

##### h. 浏览器直接访问两台tomcat，进行测试

> 但我们网站一般对外之后提供一个入口地址，所以这个时候可以使用nginx进负载。

##### i. 配置nginx

> 在 server模块里添加location，并配置proxy_pass

```
location /tomcats {
	proxy_pass http://www.tomcats.com;
}
```

其中http://www.tomcats.com字符串要和upstream后面字符串相等

##### j. 在http模块上加

> 在http模块加上upstream配置

```
upstream www.tomcats.com {
	server 127.0.0.1:8081;
	server 127.0.0.1:8082;
}
```

> 注意：默认情况负载均衡策略为轮策略，8081和8082这2台Tomcat交替处理用户请求
> **upstream是配置nginx与后端服务器负载均衡非常重要的一个模块**
> 并且它还能对后端的服务器健康状态进行检查，若后端服务器的一台发生故障，则前端的请求不会转发到该故障的机器

##### k. 重启Nginx

```
sudo /usr/local/nginx/sbin/nginx -c /www/myNginxConfigure -t
sudo nginx -s reload
```

##### l. 浏览器直接访问nginx进行测试


## 3. Nginx常用负载均衡策略

### 1) 轮询（默认）

**每个用户请求相对均匀的发送到不同的Tomcat中，比较适合使用在所有Tomcat服务器性能一致的时候使用**

```
upstream backserver {
	server xxx.xxx.xxx.xxx:8080;
	server yyy.yyy.yyy.yyy:9090;
}
```

### 2) 权重

**每个请求按一定比例分发到不同的后端服务器，weight值越大访问的比例越大，用于后端服务器性能不均的情况**

```
upstream backserver {
	server xxx.xxx.xxx.xxx:8080 weight=5;
	server yyy.yyy.yyy.yyy:9090 weight=2;
}
```

### 3) 最少连接

**web请求会被转发到连接数最少的服务器上**

```
upstream backserver {
	lease_conn;
	server xxx.xxx.xxx.xxx:8080;
	server yyy.yyy.yyy.yyy:9090;
}
```

### 4) ip_hash

> ip_hash也叫IP绑定，每个请求按访问ip的hash值分配
> 这样每个访问客户端会固定访问一个后端服务器，可以解决会话Session丢失的问题

```
upstream backserver {
	ip_hash;
	server xxx.xxx.xxx.xxx:8080;
	server yyy.yyy.yyy.yyy:9090;
}
```

### 5) 负载均衡其他几个配置

1. backup
```
upstream backserver {
	server xxx.xxx.xxx.xxx:8080;
	# 其它所有的非backup机器down的时候，才请求backup机器
	server yyy.yyy.yyy.yyy:9090 backup;
}
```

2. down
```
upstream backserver {
	server xxx.xxx.xxx.xxx:8080;
	# down表示当前的server是down状态，不参与负载均衡
	server yyy.yyy.yyy.yyy:9090 down;
}
```

*一般在项目上线的时候，可以分配部署不同的服务器上，然后对Nginx重新reload，reload不会影响用户访问，或者可以给一个提示页面，系统正在升级...*


# 六、静态代理

> 把所有静态资源的访问改为访问nginx，而不是访问tomcat，这种方式叫静态代理。因为nginx更擅长于静态资源的处理，性能更好，效率更高。所以在实际应用中，我们将静态资源比如图片、css、html、js等交给nginx处理，而不是由tomcat处理。

![[Pasted image 20230621103740.png]]

## 1. 方式一

> 在nginx.conf的location中配置静态资源的后缀

**例如：当访问静态资源，则从Liunx服务器/opt/static目录下获取**

```
location ~ .*\.(js|css|htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|pdf|xls|mp3|wma)$ {
	root /opt/static;
}
```

- 说明
	- `~` 表示正则匹配，也就是说后面的内容可以是正则表达式匹配
	- 第一个点 `.` 表示任意字符
	- `*` 表示一个或多个字符
	- `\.` 是转义字符，是后面这个点的转移字符
	- `|` 表示或者
	- `$` 表示结尾

> 整个配置表示以.后面括号里面的这些后缀结尾的文件都由nginx处理
> 放置静态资源的目录，要注意一下目录权限问题，如果权限不足，给目录赋予权限
> 否则会出现403错误，`chmod 755`

## 2. 方式二

> 在nginx.conf的location中配置静态资源所在目录实现

**例如：当访问静态资源，则从Linux服务器/opt/static目录下获取**

```
location ~ .*/(css|js|img|images) {
	root /opt/static;
}
```

- xxx/css
- xxx/js
- xxx/img
- xxx/images
*我们将静态资源放入/opt/static目录下，然后用户访问时由nginx返回这些静态资源*


# 七、动静分离

> Nginx的负载均衡和静态代理结合在一起，我们可以实现动静分离，这是实际应用中常见的一种场景。
> 动态资源，如jsp由tomcat或其他服务器完成
> 静态资源，如图片、css、js等由nginx服务器完成
> 它们各施其职 ，专注于做自己擅长的事情
> 动静分离充分利用了它们各自的优势，从而达到更高效合理的架构

## 1. 架构图

![[Pasted image 20230621134205.png]]

> 整个架构中，一个nginx负责载均衡，两个nginx负责静态代理。
> Nginx在一台Liunx上安装一份，可以启动多个Nginx，每个Nginx的配置文件不一样即可。

## 2. 实现步骤

1. 拷贝两份nginx配置文件（静态代理）

2. 修改新拷贝的 nginx-81.conf 和 nginx-82.conf 配置文件
	- nginx-81.conf 端口号
	- nginx-82.conf 端口号，这两个机器只需要做静态代理，所以删除掉负载均衡的配置
	- 静态代理的配置
	```
	location ~ .*/(css|js|img|images) {
		root /opt/static;
	}
	```

3. 负载均衡Nginx配置（nginx.conf）
	- 动态/静态资源的负载均衡
	```
	# server模块内
	# 动态资源拦截
	location /tomcats {
		proxy_pass http://www.tomcats.com;
	}
	# 静态资源拦截
	location /static {
		proxy_pass http://www.static.com;
	}
	```

	```
	# http模块内
	# 动态资源的负载均衡
	upstream www.tomcats.com {
		# ip_hash;
		server 127.0.0.1:8081;
		server 127.0.0.1:8082;
	}
	# 静态资源的负载均衡
	upstream www.static.com {
		server 127.0.0.1:81;
		server 127.0.0.1:82;
	}
	```

4. 启动三台nginx服务器，启动两台tomcat服务器进行测试。


# 八、虚拟主机

> 虚拟主机，就是把一台物理服务器划分成多个“虚拟”的服务器，这样我们的一台物理服务器就可以当多个服务器来使用，从可以配置多个网站。
> Nginx提供虚拟主机的功能，就是为了让我们不需要安装多个Nginx，就可以运行多个域名不同的网站。
> Nginx下，一个server标签就是一个虚拟主机。nginx的虚拟主机就是通过nginx.conf中server节点指定的，想要设置多个虚拟主机，配置多个server节点即可；

## 1. 配置虚拟主机方式

### 1) 基于端口的虚拟主机

> 基于端口的虚拟主机配置，使用端口来区分；浏览器使用同一个域名+端口 或 同一个ip地址+端口访问；

```
server {
	listen 8080;
	server_name www.example.com;
	location /demo {
		proxy_pass http://www.demo.com;
	}
}
```

```
server {
	listen 8090;
	server_name www.example.com;
	location /demo {
		proxy_pass http://www.demo.com;
	}
}
```

### 2) 基于域名的虚拟主机（推荐）

> 基于域名的虚拟主机是最常见的一种虚拟主机

```
server {
	listen 80;
	server_name www.example01.com;
	location /web {
		proxy_pass www.example01.com;
	}
}
```

```
server {
	listen 80;
	server_name www.example02.com;
	location /web {
		proxy_pass www.example02.com;
	}
}
```

> 需要修改一下本地的host文件，文件位置：`C:\Windows\System32\drivers\etc\hosts`
> 在hosts文件中配置：
> `127.0.0.1 www.example01.com`
> `127.0.0.1 www.example02.com`
> 前面是Linux的IP，后面是自定义的域名

## 2. 案例

1. 创建两个vhost.xxx.conf并编写一下内容
```
# vhost-demo01.conf

upstream www.demo01.com {
	server 192.168.1.58:8081;
}
server {
	listen 80;
	server_name www.demo01.com;
	location /demo01 {
		proxy_pass http://www.demo01.com;
	}
	location ~ .*/(css|js|img|images) {
		root /opt/static/;
	}
}
```

```
# vhost-demo02.conf

upstream www.demo02.com {
	server 192.168.1.58:8082;
}
server {
	listen 80;
	server_name www.demo02.com;
	location /demo01 {
		proxy_pass http://www.demo02.com;
	}
	location ~ .*/(css|js|img|images) {
		root /opt/static/;
	}
}
```

2. 在nginx.conf中包含两个vhost-xxx.conf文件
```
# 与server同级
include /www/myNginxConfig/vhost-demo01.conf;
include /www/myNginxConfig/vhost-demo02.conf;
```

3. 修改`C:\Windows\System32\drivers\etc\hosts`文件
```
192.168.1.58 www.demo01.com
192.168.1.58 www.demo02.com
```

4. 地址栏输入测试。

