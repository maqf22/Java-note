
# 一、Docker简介

开发环境 ==> 测试环境 ==> 生产环境
Docker是一个开源的应用容器引擎，可以帮我们解决因为环境不同而导致项目水土不服的问题。
- 容器采用沙箱机制，相互隔离
- 容器性能开销低，不吃硬件


# 二、Docker的安装

> Docker支持在Windows、CentOS、Ubantu、MacOS等操作系统上使用

## 1. CentOS中安装

1. yum包更新  `yum update`
2. 安装必要的依赖包  `yum install -y yum-utils device-mapper-persistent-data lvm2`
3. 设置Docker的yum源
	`yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`
4. 安装Docker `yum install -y docker-ce`
5. 查看Docker版本号 `docker -v`

## 2. Ubuntu(subsystem)中安装步骤

> 但是不要安装在子系统中，由**子系统**并非独立系统的原因
> **systemctl等命令无法正常运行**，需要通过其他复杂的方式作出调整

1. 更新apt包的索引 `sudo apt-get update`
2. 安装apt依赖包，用于通过https来获取仓库 `sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common`
3. 添加Docker的官方GPG密钥 `curl -fsSl https://downlaod.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
4. 查看相关秘钥信息（可做可不做） `sudo apt-key fingerprint`
5. 设置稳定版本仓库 `sudo add-apt-repository "deb [arch=amd64] https://download/docker.com/linux/ubuntu $(lsb_release -cs) stable}"`
6. 再次更新apt包索引 `sudo apt-get update`
7. 列出可用版本 `apt-cache madison docker-ce`
8. 安装 `sudo apt-get install docker-ce=5:20.10.13~3-0~ubuntu-focal docker-ce-cli=5:20.10.13~3-0~ubuntu-focal containerd.io`
9. 测试安装是否成功，查看版本号 `docker -v`

## 3. Mac中的安装

1. 打开控制台并输入指令 `brew install --cask --appdir=/Applications docker`
2. 在启动台中找到刚安装好的docker，并点击启动，之后需要输入你的Mac密码
3. 在弹出的协义对话框中选中允许即可
4. 安装成功后会在上方的工具栏看到已启动的docker图标

> 或者直接通过官网下载dmg安装包即可。


# 三、Docker镜像配置

> 这里使用阿里云提供的镜像加速器
> https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

![[Pasted image 20230717153831.png]]


# 四、Docker的架构

![[Pasted image 20230717154130.png]]


# 五、Docker命令

> 更多详细命令参考 https://www.runoob.com/docker/docker-command-manual.html

## 1. 常用服务命令

命令 | 说明
:- | :-
`systemctl status docker` | 查看状态
`systemctl start docker` | 启动服务
`systemctl stop docker` | 停止服务
`systemctl restart docker` | 重启服务
`systemctl enable docker` | 开机启动

## 2. 常用镜像命令

命令 | 说明
:- | :-
`docker images` | 查看镜像
`docker search xxx` | 搜索镜像
`docker pull xx[:version]` | 拉取镜像
`docker rmi <IMAGE ID>` | 删除镜像

> 可通过 https://hub.docker.com/ 查看Docker支持的版本号

## 3. 常用容器命令

> 通过镜像来创建容器

- 查看容器
	- `docker ps` 查看正在运行的容器
	- `docker ps -a` 查看所有的容器
`案例：`
![[Pasted image 20230717164626.png]]

- 创建容器
	- `docker run <参数>`
		- `-i`  保持容器运行，通常与 -t 连用，加入 it 两个参数后，容器创建后会自动进入到容器中，退出后，容器自动关闭
		- `-t`  为容器重新分配一个新输入终端，相当于一个shell，通常与 -i 联合使用
		- `-d`  以守护模式（后台）运行容器，创建一个容器并在后台保持运行，需要使用 `docker exec` 进入容器，退出后不会关闭
		- `-it`  交互式容器
		- `-id`  守护式容器
		- `--name`  为创建的容器命名
`案例：`
1. 拉取CentOS镜像到Docker
```shell
docker pull centos:7
docker images
```
2. 创建一个容器centos01并进入容器
```shell
docker run -it --name=centos01 centos:7 /bin/bash
```
![[Pasted image 20230717171632.png]]
3. 通过exit退出容器，并查年容器
![[Pasted image 20230717171709.png]]
4. 创建容器并在后台启动容器，（命令下方显示的是容器创建之后的容器ID）
```shell
docker run -id --name=centos02 centos:7
```
![[Pasted image 20230717171930.png]]
5. 查看容器
```shell
docker ps [-a]
```
![[Pasted image 20230717172034.png]]
6. 进入后台正在运行的容器
```shell
docker exec -it [容器名] /bin/bash
```
7. 退出容器控制台（容器并不会停止）
![[Pasted image 20230717172816.png]]
8. 关闭容器
```shell
docker stop 容器名
```
9. 启动容器
```shell
docker start 容器名
```
10. 删除容器
```shell
docker rm 容器名
```
11. 删除所有容器
```shell
docer rm `docker ps -aq`
```
12. 查看容器的详细信息
```shell
docker inspect 容器名
```


# 六、Docker容器数据卷

数据卷就是放数据用的，使用数据卷，可以实现宿主和容器之间的数据共享，实现数据的持久化
- 数据卷是宿主机中的目录或者文件
- 当容器目录和数据卷目录绑定后，只要修改就会立即同步
- 一个数据卷可以被多个容器挂载
- 一个容器也可以被挂载多个数据卷

## 1. 配置数据卷

- 创建启动容器时，使用 -v 参数设置数据卷
```shell
# docker run -it --name=c02 -v 宿主机目录/文件:容器内目录/ centos:7
docker run -it --name=c03 -v /root/myData:/root/myDataContainer centos:7
```
![[Pasted image 20230717175504.png]]
> 目录如果不存在则会被自动创建 ，设置数据卷之后，宿主机和容器中两端数据同步
> 容器关闭或者删除之后，数据卷目录不会被删除

- 挂载多个数据卷
```shell
docker run -it --name=c04 \
> -v /root/myDataA:/root/myDataA \
> -v /root/myDataB:/root/myDataB \
> -v /root/myDataC:/root/myDataC \
> centos:7
```
![[Pasted image 20230717180828.png]]

- 多个容器挂载同一个数据卷
```shell
docker run -it --name=c05 -v /root/myData:/root/myData centos:7
```
```shell
docker run -it --name=c06 -v /root/myData:/root/myData centos:7
```

## 2. 数据卷容器

> 数据卷容器可以讲将个容器更方便地挂载到相同的数据卷上
> 相当于是个向导，并支持卸磨杀驴

1. 第一步
```shell
docker run -it --name=ct -v /root/myData:/root/myData centos:7
```
2. 第二步
```shell
docker run -it --name=c01 --volumes-from ct centos:7
docker run -it --name=c02 --volumes-from ct centos:7
```
3. 测试


# 七、Docker应用部署

> 容器内的网络服务和外部的访问终端是不能直接通信的，需要通过**端口映射**的方式实现访问

## 1. MySQL

- `my.cnf`
```cnf
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
collation-server=utf8_unicode_ci
init-connect='SET NAMES utf8'
character_set_server=utf8

general_log=ON
general_log_file=/var/log/mysql/mysql.log
```

```shell
docker pull mysql:5.7

docker run -id -p 3306:3306 \
> --name=cMySQL \
> -v /root/mysql/conf:/etc/mysql \
> -v /root/mysql/logs:/var/log/mysql \
> -v /root/mysql/data:/var/lib/mysql \
> -e MYSQL_ROOT_PASSWORD=root \
> mysql:5.7

docker exec -it cMySQL /bin/bash
```

## 2. Redis

```shell
docker pull redis

docker run -id --name=cRedis -p 6379:6379 redis
docker exec -it cRedis /bin/bash
```

## 3. Tomcat

```shell
docker pull tomcat:9

mkdir /root/tomcat/ROOT

docker run -id --name=cTomcat \
-p 8080:8080 \
-v /root/tomcat/ROOT:/usr/local/tomcat/webapps \
tomcat:9

docker exec -it cTomcat /bin/bash
```


# 八、Dockerfile

## 1. 将容器转换为镜像

命令 | 说明
:- | :-
`docker commit 容器id 镜像名称：版本号` | 将容器保存为镜像
`docker save -o 压缩文件名 镜像名称：版本号` | 将镜像保存为压缩文件
`docker load -i 压缩文件名称` | 将压缩文件还原成镜像文件

> 使用`docker commit 保存的镜像，只能在保存镜像内部发生的变化，这里不包括之前通过镜像创建容器过程中挂载的数据卷

## 2. 使用Dockerfile

> dockerfile是一个由各种指令组成的文本文件。指令适用于构建镜像的。通常用于避免开发、测试 、 运维环境水土不服问题 

- 案例：自定义CentOS7镜像，默认登录路径为/root，集成Vim和ifconfig命令

```dockerfile
FROM centos:7

MAINTAINER maqf maqf@maqf.com

WORKDIR /root

RUN yum install -y vim
RUN yum install -y net-tools

CMD /bin/bash
```

> 使用 `docker build -f dockerfile文件名 -t 镜像名:版本号 .`  通过dockerfile文件创建镜像
> 使用镜像创建容器并进入测试 `docker run -it --name=容器名 镜像名:版本号`

```shell
docker build -f myDockerfile.dockerfile -t mycentos:7 .
```

- 案例：部署一个SpringBoot项目到docker

```dockerfile
FROM java:8

MAINTAINER maqf maqf@maqf.com

ADD dockerSpringBoot-9527.jar app.jar

CMD java -jar app.jar
```

```shell
docker build -f mySpringBoot.dockerfile -t app:1.0 .
```

`测试 192.168.10.128:8080`


# 九、Docker Compose

> 服务编排，主要应用于微服务。使用Docker Compose进行统一批量的管理
> Docker Compose是一个编排多容器分布式部署的工具，提供命令脚本管理
> 容器化应用的完整开发流程，包括服务构建、启动、停止服务

## 1. Docker Compose安装与卸载

- 安装
```shell
# 下载到本地
curl -L https://github.com/docker/compose/releases/download/1.29.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

# 添加访问权限
chmod a+x /usr/local/bin/docker-compose

# 查看版本号
docker-compose -version
```

- 卸载
```shell
# 删除下载目录即可
rm /usr/local/bin/docker-compose -rf
```

## 2. 使用步骤

1. 使用`Dockerfile`定义运行环境
![[Pasted image 20230718152735.png]]
3. 使用`docker-compose.yml`
4. 运行`docker-compose up`启动应用

- 案例-搭建基于Nginx反向代理访问的SpringBoot项目
5. 创建一个用于测试的`docker-compose`工作目录
```shell
mkdir /root/docker-compose/nginx/conf.d -p
cd /root/docker-compose
```
2. 编写`docker-compose.yml`配置文件
```yml
version: '3.5'
services:
	nginx:
		image: nginx
		ports:
			- 80:80
		links:
			- app
		volumes:
			- ./nginx/conf.d:/etc/nginx/conf.d
	app:
		image: app:1.0
		expose:
			- "9527"
```
3. 编写`/root/docker-compose/nginx/conf.d/nginx.conf`配置文件
```conf
server {
	listen 80;
	access_log off;

	location / {
		proxy_pass http://app:9527;
	}
}
```
4. 通过`docker-compose up`命令启动应用
5. 通过浏览器访问：`http://192.16.1.128`


# 十、Docker私有仓库

## 1. 创建私有仓库

```sh
#!/bin/bash
# 创建docker私有仓库
# 拉取私有仓库功能镜像
docker pull registry
# 启动私有仓库容器
docker run -id --name=registry -p 5000:5000 registry
# 添加私有仓库信任配置到json配置文件
sudo tee /etc/docker/daemon.json <<-'EOF'
{
	"registry-mirrors":["https://6jjvg109.mirror.aliyuncs.com"]
	"insecure-registries":["192.168.10.128:5000"]
}
EOF
# 重新加载daemon.json配置文件
systemctl daemon-reload
# 重启docker服务
systemlctl restart docker
# 启动容器
docker start registry
```

## 2. 上传镜像到私有仓库

```sh
# 标记镜像为私有仓库镜像
docker tag centos:7 192.168.10.128:5000/centos:7

# 上传标记的镜像
docker push 192.168.10.128:5000/centos:7
```

## 3. 从私有仓库拉取镜像

```shell
docker pull 192.168.10.128:5000/centos:7
```

> 更多使用方法详见Docker官网 https://docs.docker.com/get-started/





















