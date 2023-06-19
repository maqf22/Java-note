
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

1. 

