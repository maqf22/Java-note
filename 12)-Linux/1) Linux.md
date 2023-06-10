
# 一、Linux常用命令

## 1. 命令格式

格式：命令 -选项参数 \[操作对象\]

## 2. 文件处理命令

### 1) ls

- 命令英文意思：list
- 执行权限描述：所有用户
- 执行功能描述：显示目录或文件
- 语法：`ls [-alh] [文件或目录]`
- 参数：
	- -a 显示所有文件，包括隐藏文件
	- -l 以长格式显示文件详细信息
	- -h 以适当的单位显示文件大小，与 -l 参数连用

### 2) cd

- 命令英文意思：change directory
- 执行权限描述：所有用户
- 执行功能描述：切换目录
- 语法：`cd [目录名]`

### 3) pwd

- 命令英文意思：print work directory
- 执行权限描述：所有用户
- 执行功能描述：显示当前工作目录

### 4) mkdir

- 命令英文意思：make directories
- 执行权限描述：所有用户
- 执行功能描述：创建新目录
- 语法：`mkdir [参数] <目录名>`
- 参数：
	- -p 递归创建

### 5) touch

- 命令所在路径：/bin/touch
- 执行权限描述：所有用户
- 执行功能描述：创建空文件
- 语法：`touch newFile`

### 6) cp

- 命令英文意思：copy
- 执行权限描述：所有用户
- 执行功能描述：复制文件或目录
- 语法：`cp [参数] [源文件或目录] [目标目录]`
- 参数：
	- -r 复制目录

### 7) mv

- 命令英文意思：move
- 执行权限描述：所有用户
- 执行功能描述：移动文件、改名
- 语法：`mv [参数] [源文件或目录] [目标目录]`

### 8) rm

- 命令英文意思：remove
- 执行权限描述：所有用户
- 执行功能描述：删除目录或文件
- 语法：`mv [参数] [目录或文件]`
- 参数：
	- -f 强制删除，不询问
	- -r 递归删除

### 9) cat

- 执行权限描述：所有用户
- 执行功能描述：显示文件内容
- 语法：`cat fileName`

### 10) more

- 执行权限描述：所有用户
- 执行功能描述：分页显示文件内容
- 语法：`more fileName`
- \<空格\> 或 f 翻页
- \<enter\>下一行
- q 或Q退出

### 11) ln

- 命令英文意思：link
- 执行权限描述：所有用户
- 执行功能描述：生成连接文件
- 语法：`ln [参数] [源文件] [目标文件]`
- 参数：
	- -s 生成软连接

## 3. 权限管理命令

### 1) chmod

- 命令英文意思：change the permissions mode of a file
- 执行权限描述：所有用户
- 执行功能描述：
- 语法：
	1. `chmod <[ugoa]>[+-=][rwx] <文件或目录>`
		- u -> user  文件所有者
		- g -> group  文件所属组
		- o -> other  其他用户
		- a -> all  所有用户
		- + -> 添加权限
		- - -> 删除权限
		- = -> 设置权
	2. `chmod <mode> <文件或目录>`
		- rwx rwx rwx -> 777
		- 111 111 111
		- r-x -> 5
		- 101
		- rw- -> 6
		- 110
		- -r- -> 2
		- 010
		- (rwx)
		- (421)
- 参数：
	- -R 递归设置

### 2) chown

- 命令英文意思：change file ownership
- 执行权限描述：所有用户
- 执行功能描述：改变文件或目录的所有者.所属组
- 语法：`chown <userName[.groupName]> fileName`
- 参数：
	- -R 递归设置

### 3) chgrp

- 命令英文意思：change file group ownership
- 执行权限描述：所有用户
- 执行功能描述：改变文件或目录的所属组
- 语法：`chgrp <groupName> fileName`

## 4. 文件搜索命令

### 1) which

- 执行权限描述：所有用户
- 执行功能描述：查看系统命令的所在目录
- 语法：`which <command>`

### 2) find

- 执行权限描述：所有用户
- 执行功能描述：文件搜索
- 语法：`find [搜索范围] [匹配文件]`
- EX：
	1. `find /etc -name init`  在etc中查找文件名为init的文件或目录，其中可以使通配符*或?
	2. `find / -size +204800`  在根目录下查找大于100MB的文件
	3. `find /home -user maqf`  在home目录下查找所有者为maqf的文件
	4. `find /etc -cmin -5`  在etc下查找5分钟内修改过属性的文件或目录
		- `-amin` 访问时间access
		- `-cmin`文件属性change
		- `-mmin`文件内容modify
	5. `find /etc -size +163840 -a -size -204800`  在etc下查找大于80MB并且小于100MB的文件

### 3) grep

- 执行权限描述：所有用户
- 执行功能描述：在文件中搜索字符串匹配的行并输出
- 语法：`grep -iv [指定字符串] [文件]`
- 参数：
	- -i 不区分大小写
	- -v 排队指定字符串


## 5. 帮助命令

### 1) man

- 命令英文意思： manual
- 执行权限描述：所有用户
- 执行功能描述：获取帮助信息
- 语法：`man <command>`

> https://www.linuxcool.com/


## 6. 压缩/解压缩/打包命令

### 1) gzip

- 命令英文意思： GNU zip
- 执行权限描述：所有用户
- 执行功能描述：压文件（压缩格式.gz）
- 语法：`gzip <fileName>`
- 注：*只参对文件进行操作，且执行后不保留源文件*

### 2) gunzip

- 命令英文意思： GNU unzip
- 执行权限描述：所有用户
- 执行功能描述：解压缩.gz格式的压缩文件
- 语法：`gunzip <fileName.gz>`

### 3) bzip2

- 执行权限描述：所有用户
- 执行功能描述：压缩文件（压缩格式.bz2）
- 语法：`bzip2 [选项] <fileName>`
- 参数：
	- -k：保留源文件

### 4) bunzip2

- 执行权限描述：所有用户
- 执行功能描述：解压缩.bz2格式的压缩文件
- 语法：`bunzip2 [选项] <fileName.bz2>`
- 参数：
	- -k：保留源文件

### 5) tar

- 执行权限描述：所有用户
- 执行功能描述：打/解包目录
- 语法：`tar <选项> <目标文件名> <源文件>`
- 参数：
	- -c  打包
	- -x  解包
	- -v  显示详细信息
	- -f  指定文件名 ***
	- -z  打包同时压缩.gz
	- -j  打包同时压缩.bz2


## 7. 网络通信命令

### 1) ping

- 执行权限描述：所有用户
- 执行功能描述：测试网络连接
- 语法：`ping <ip address>`
- 参数：
	- -c  指定发送次数
	- -s  指定数据包大小

### 2) ifconfig

- 命令英文意思：interface configure
- 执行权限描述：root
- 执行功能描述：查看和设置网卡信息
- 语法：
	1. `ifconfig <Enter>`  查看网络
	2. `ifconfig eth0 <ip address>`  设置eth0网卡的IP地址


## 8. 关机命令

### 1) shutdown 

- 执行权限描述：root
- 执行功能描述：关机
- 语法：`shutdown -h now`
- 等价于：`init 0`

### 2) reboot

- 执行权限描述：root
- 执行功能描述：重启
- 语法：`reboot <Enter>`
- 等价于：`init 6`


# 二、VIM

## 1. 三种工作模式

- 命令模式：在命令模式下可以对当标进行快速的定位、复制、粘贴等操作
- 编辑模式：在编辑模式下可以对本文内容进行编辑
- 末行模式：在末行模式下可以对文本内容进行保存、查找等附加功能

## 2. 定位命令

命令 | 作用 | 命令 | 作用
:- | :- | :- | :-
:set nu | 设置行号 | :set nonu | 取消行号
gg | 定位到第一行 | G | 定位到最后一行
ngg/nG | 定位到第n行 | :n | 定位到第n行
$ | 定位到末行 | 0 | 定位到行首
h | 左 | l | 右
J | 下 | k | 上

## 3. 插入命令

命令 | 作用 | 命令 | 作用
:- | :- | :- | :-
a | 当前光标所在字符后 | A | 当前光标所在行末
i | 当前光标所在字符前 | I | 当前光标所在行首
o | 当前所在行下插入行 | O | 当前所在行上插入行
s | 替换当前光标所在处 | S | 替换当前当标所在行

## 4. 删除命令

命令 | 作用 | 命令 | 作用
:- | :- | :- | :-
x | 删除光标所在处字符 | nx | 删除光标所在处后n个字符
dd | 删除当前当标所在行 | dG | 删除光标所在处到文件末尾所有内容
D | 删除当前光标所在处到行末 | :n,nd | 删除指定范围的行

## 5. 复制和剪切命令

命令 | 作用
:- | :-
yy | 复制当前光标所在行
nyy | 复制当前行以下n行（包含当前行）
dd | 剪切当前光标所在行
ndd | 剪切当前行以下n行（包括当前行）
v | 可视，用于复制或剪切删除时选取操作，配合y/d/p
p/P | 粘贴在当前行下方/上方

## 6.  替换和撤销命令

命令 | 作用
:- | :-
r | 取代光标所在处字符
R | 从光标所在处开始替换字符，知道ESC键结束
u | 取消上一步操作

## 7. 搜索和替换命令

命令 | 作用
:- | :-
/stirng | 搜索指定字符串，搜索时忽略大小写：set ic
n/N | 从当前位置搜索指定字符串的下一个/上一个出现的位置
:%s/old/new/g | 全文替换new到old字符串
:n1,n2s/old/new/g | 在一定范围内替换new到old字符串

## 8. 保存和退出命令

命令 | 作用
:- | :-
:w | 保存修改
:w filename | 另存为新的指定文件
:wq | 保存修改并退出
ZZ | vim中保存退出指令
:q! | 强制不保存退出
:wq! | 针对只读文件，强制保存退出（文件所有者及root用户可用）

## 9. 输出重定向

- 把指定的内容重新定向的指定的文件中
	- `>`  覆盖方式的写入
	- `>>`  追加方式的写入

## 10. vim的个性化配置

> 编辑当前用户字目中的.vimrc配置文件

```vimrc
set nu
set ts=4
set autoindent
```

> history 用于查历史指令
> ctrl + r 模糊搜索历史指令


# 三、网络命令

## 1. IP地址配置

- 使用命令配置IP地址

`ifconfig eth0 xxxx.xxxx.xxxx.xxxx`  为eth0网卡设置IP地址（临时生效）

## 2. 网卡配置文件

- `/etc/sysconfig/network-scripts/ifcfg-eth0`  网卡信息配置文件
- `/etc/sysconfig/network`  主机名配置文件，永久生效，需重启网卡
	- HOSTNAME=localhost.localdomain
- `hostname <主机名>`  临时设置主机名，可直接通过hostname命令查看当前主机名
- `/etc/resolv.conf`  DNS配置文件，也可通过`setup`命令配置DNS
	- nameserver 114.114.114.114

## 3. 常用命令

命令 | 功能
:- | :-
ifconfig | 查看网卡信息
ifup eth0 | 快速启动eth0网卡
ifdown eth0 | 快速关闭eth0网卡
netstat -an | 查看所有网连接
netstat -tlun | 查看tcp和udp协议监听端口
ping xxxx.xxxx.xxxx.xxxx | 检查网络畅通性
setup | 使用图形界面配置网络信息


# 四、Linux软件包管理

## 1. 源码包

- 优点：开源自由定制，效率更高
- 缺点：编译时长，一旦报错，很难解决

## 2. 二进制包

- 优点：安装速度快（简易）
- 缺点：自定义差

## 3. 挂载命令

### 1. 挂载U盘

1. 将U盘插入本机并识别
2. 将被本机识别的U盘连接到虚拟机，虚拟机-可以动设备-你的U盘
3. `fdisk -l` 查看当前系统中的文件系统列表（查看磁盘分区）
4. `mkdir /mnt/usbDisk` 创建一个新的挂载点
5. `mount /dev/sdXX /mnt/usbDisk` 将U盘设备挂载到新建的挂载点上
6. 进入挂载点对U盘中文件进行操作
7. 退出U盘挂载目录
8. `umount usbDisk`或`umount /dev/sdXX` 卸载U盘
9. 在虚拟机中设置断开U盘连接，将U盘还给windows
10. 当windows识别U盘后，可在windows中对U盘进行各种操作

## 4. rmp包安装

- 安装

命令 | 功能
:- | :-
rpm -ivh 软件包 | 安装软件
rpm -Uvh 包全名 | 升级软件

> 其中参数：i 安装，v 显示相关信息，h 以进号显示进度条，U 升级

www.rpmfind.net rpm依赖包查找

- 卸载

命令 | 功能
:- | :-
rpm -e 软件名 | 卸载软件包（--nodeps不检查依赖关系）

- 查询

命令 | 功能
:- | :-
rpm -q 软件名 | 查询软件包是否被安装
rpm -qa | 查询所有已安装的软件包
rpm -qi 软件名 | 查询包信息
rpm -qip 软件包 | 查询没有安装的包信息
rpm -ql 软件包 | 查询包中文件已安装的位置
rpm -qlp 软件包 | 查询没做安装的包，打算安装位置
rmp -qf | 系统文件名查询系统文件属于哪个包

> service xxx start/stop

## 5. yum命令

命令 | 功能
:- | :-
yum -y install 软件包 | 安装软件
yum -y remove 软件包 | 卸载软件
yum -y update 软件包 | 升级软件包
yum list | 查询软件包

**使用光盘作为yum源**

- 进入yum源配置文件存放目录：`/etc/yum.repos.d/`
- 将除CentOS-Media.repo以外的所有文件删除或改名（推荐改名），目的让其他yum源失效
- 将光驱挂到Linux系统当中，记住挂在路径
- 编辑CentOS-Media.repo配置文件，并修改一下内容

```repo
baseurl=file:///mnt/cdrom/  # 修改为光驱挂在路径
enabled=1  # 设置yum源文件生效
gpgcheck=0  # 设置rpm验证不生效
```

- 保存退出后，可使用`yum list`命令查看yum源设置是否成功
- 设置成功后EX：`yum -y install gcc`和`yum -y install gcc-c++`

## 6. 源码包安装

1. 解压、解包
2. 进入源码包目录
3. 查看安装文件：INSTALL和README
4. 编译前的准备：./configure（安装前的配置）
5. 检测系统环境，生成MakeFile文件
6. 定义软件选项
7. 编译make
8. 编译安装make install
- 报错：
	- 安装过程是否停止
	- 注意error、warning、no等错误报警
	- make clean


# 五、Linux进程管理

## 1. 进程管理三个主要任务

1. 判断服务器状态
2. 查看所有正在运行的进程
3. 强制结束进程

## 2. 查看进程

- 查看当前系统的所有进程：`ps aux`
	- -a：显示前台所有进程
	- -u：显示用户名
	- -x：显示后台进程
		- user：用户名
		- pid：进程ID（PID 1 init系统启动的第一个进程）
		- %CPU：cpu占用百分比
		- %MEM：内存占用百分比
		- VSZ：虚拟内存占用量，单位KB
		- RSS：固定内存占用量
		- tty：登录终瑞（tty1-7其中1-6为本地字符终端7为图形终端，或者pty/pts/0-5是伪终端）
		- stat: 状态
			- S：睡眠
			- D：不可唤醒
			- R：正在运行
			- T：停止
			- **Z：僵死**
			- W：进入内存交换
			- **X：死掉的进程**
			- \<：高优先级的进程
			- N：低优先级的进程
			- L：被缩进内存的进程
			- s：包含子进程
			- +：位于后台的进程
			- l：具有多线程的进程
		- start：进程的触发时间
		- time：占用cpu时间
		- command：进程命令

- 查看进程树`pstree`

- 动态查看进程硬件占率top
	- M：按照内存使用情况进行排序
		- P：按照cpu情况进行排序
		- q：退出

## 3. 进程管理

### 1) 终止进程

- kill命令
```linux
kill PID -9  # -9 表示强制结束进程
```

- pstree
```linux
killall -9 进程名`  # 结束一类进程
pkill -9 进程名  # 结束一个进程
pkill -9 -t 终端号  # 把某个终端登录的用户踢出
pkill -9 -t tty1  # 把本地登录终端1用户踢出
```

### 2) 循环定时任务

```linux
crontab -e  # 编辑定时任务
crontab -l  # 查看系统定时任务
crontab -r  # 删除定时任务

格式：* * * * * 命令
其中：
	第1位：一小时中第几分钟  0 - 59
	第2位：一天中第几个小时  0 - 23
	第3位：一个月中的第几天  1 - 31
	第4位：一年中的第几个月  1 - 12
	第5位：一周中的第几天    0 - 6

examples:
10 * 31 * * 命令   # 每个月31号的每个小时里的第10分钟要执行一个命令
10 * * * * 命令   # 每个小时的第十分钟要执行一个命令
5 4 * 5,7,10 * 命令   # 每年的5、7、10月的4点5分要执行一个命令
*/10 * * * * 命令   # 每10分钟执行一个命令
```

> [!注意事项]
> *定时任务选项都不能为空，必须填入；*
> *不知道的值使用通配符\*表示任何时间；*
> *每个时间段都可以提定多个值，不连续的值使用逗号间隔；*
> *连续的值用减号间隔，间隔固定时间执行书写为\*/n格式；*
> *命令应该给出绝对路径，星期几和第几天不要同时出现；*
> *最小时间范围是分钟，最大时间范围是月*


# 六、Linux服务管理

## 1. Linux中的服务分类

1. 系统默认安装的服务
2. 源码包安装的服务

## 2. 系统默认安装的服务

- 确定服务分类
	- `chkconfig --list` 查看服务的自启状态
	- 运行级别：0 - 6
		- 0：关机
		- 1：单用户模式
		- 2：不完全的多用户模式，不包含NFS服力，无网络登录
		- 3：完全的多用户字符界面
		- 4：未分配（保留）
		- 5：图形界面（启动图形界面可使用`startx`或`init 5`）
		- 6：重新启动
	- runlevel：查询系统当前运行级别
	- 配置文件：/etc/inittab

- 独立的服务管理器
	- 启动服务
		1. `/etc/rc.d/init.d/服务名 start | stop | restart | status`
		2. `service 服务名 start | stop | restart | status`
	- 设置自启动
		1. `chkconfig --level 2345 服务名 on | off`
		2. 编辑配置文件：`/etc/rc.local -> /etd/rc.d/rc.local`
	- `ntsysv`命令（Redhat系列可用）
		- 所有系统默认安装的服务都可以通过`ntsysv`命令进行自启动管理


# 七、Linux文件服务器

> FTP服务器（File Transfer Protocol Serveer）是在互联网上提供文件存储和访问服务的计算机，它们依照FTP协议提供服务。FTP是File Transfer Protocol（文件传输协议）。顾名思义，就是专门用来传输文件的协议。简单地说，支持FTP协议的服务器就是FTP服务器

- ftp：在内网和公网使用
	- 服务器：Windows Liunx
	- 客户端：Windows Linux

## 1. 安装

`yum -y install vsftpd`

## 2. 配置文件

- `/etc/sysconfig/selinux` **关闭selinux之后ftp才能正常连接**
- `/etc/vsftpd/vsftpd.config` ftp配置文件
- `/etc/vsftpd/ftpusers` 用户访问控制
- `/etc/vsftpd/chroot_list` 需要手动创建，用于家目录限制

## 3. 配置文件修改

- `local_enable=YES` 允许系统用户登录
- `write_enable=YES` 允许上传
- `local_umak=022` 默认上传权限
- `local_max_reate=300` 上传限速
- 主机相关配置
	- `Listen_port=21` 监听端口
	- `connect_from_port_20` 数据传输端口
	- `ftpd_banner` 欢迎信息
	- 匿名用户登录（在Linux中识别为ftp用户）
	- `anonymous_enable=YES` 允许匿名用户登录
- **限制用户访问目录**
	- `chroot_local_user=YES`
		- **注：如果只有此句生效，所有用户限制在家目录**
	- `chroot_list_enable=YES`
	- `chroot_list_file=/etc/vsftpd/chroot_list`
	- `allow_writeable_chroot=YES`
		- **注：如果以上都生效，只有chroot_list文件中的用户可以访问其他目录，其他用户限制在家目录当中**


## 4. ftp客户端的使用

1. 在MS-DOS中登录ftp
	- `ftp xxxx.xxxx.xxxx.xxxx` 连接ftp服务器
	- `get filename` 下载文件
	- `put filename` 上传文件
	- `help`或`?`查看ftp指令

> 注意：上传和下载操作不支持目录

2. 在windows中登录ftp
3. 使用第三方工具登录


## 5. Samba文件服务器

> samba服务器是一种在局域网中常用的共享文件服务器
> 使用该服务器需要smbd和nmbd两种服务的支持

- smb为client提供资源访问 tcp 139 445
- nmb提供netbios主机名解析 udp 134 138

## 6. 安装相关的软件包

1. 配置yum安装环境
2. `yum -y install samba`

## 7. Samba配置文件

`/etc/samba/smb.conf`配置文件中有两种注释风格分别为`#`和`;`

## 8. 共享目录设置

> share definitions

- \[目录名\] \#非路径
- comment = 目录描述
- browseable = 目录是否对用户可见
- writable = 可写（与系统目录权限相对应）
- valid users = 用户限制（针对于哪个用户可用）
- path = 目录（指定的共享目录位置）

## 9. 实例

> 实现两个目录的共享，一个是pub目录，位置/pub，所有用户都能进行访问、上传；
> 另一个是soft目录，位置/soft，只有maqf用户可以访问、上传、其他用户不能对其访问；

实现步骤

1. 编辑配置文件`/etc/samba/smb.conf`

2. 在配置文件中添加内容
```conf
[pub]
	browseable = yes
	path = /pub
	writable = yes
[soft]
	browseable = yes
	path = /soft
	writable = yes
```

3. 在终端中执行以下命令

```cmd
mkdir /pub
mkdir /soft
chmod 777 /pub
chmod 700 /soft
useradd maqf
passwd maqf
chown maqf /soft
```

4. 把系统用户声明为Samba用户

```
smbpasswd -a maqf  # 声明一个系统用户为Samba用户
smbpasswd -x username  # 删除一个Samba用户
pdbedit -L  # 查看Samba用户
```

5. 重启服务 

```
service smb restart
service nmb restart
```

6. 测试搭建结果

- 在winodws中使用共享登录方式：`\\192.168.2.59`（`net use * /del`清除登录状态）
- 在Linux中登录指令：`smbclient //192.168.2.59/soft -U maqf`


# 八、Linux用户管理

## 1. 用户管理相关配置文件

### 1) `/etc/passwd`

> 用于查看用户信息

- 时间戳换算日期`date -d "1970-01-01 17056 days"`
- 日期换算时间戳`echo $(($(date --date="2023-06-09" +%s) / 8400 + 1))`
- 配置文件内容
	- 第一列：用户名
	- 第二列：密码状态
	- 第三列：用户UID
		- 0：root用户（超级用户）
		- 1-499：伪用户（系统用户）
		- 500-65535：普通用户
	- 第四列：初始组ID
	- 第五列：用户说明信息
	- 第六列：用户的家目录所在路径
	- 第七列：用户使用的指令解析器

### 2) `/etc/shadow`

> 影子文件，用于存放用户的密码信息

- 配置文件内容
	- 第一列：用户名
	- 第二列：加密后的密码串
		- 加密算法升级为SHA512散列加密算法
		- 如果密码是以`!!`或`*`代表没有密码或密码不可用，不允许登录
	- 第三列：密码最后一次修改日期
	- 第四列：两次密码的修改间隔时间（与第三列相比）
	- 第五列：密码有效期（与第三列相比）
	- 第六列：密码修改到期的警告天数（与第五列相比）
	- 第七列：密码过期后的宽限天数（与第五列相比）
		- 0：表示密码过期后立即失效
		- -1：表示密码永不过期
	- 第八列：账号失效时间（用时间戳表示）
	- 第九列：保留

### 3) 其他文件

- `/etc/group` 用于查看用户的组信息
- `/etc/gshadow` 用于查看用户的组密码信息
- `/etc/skel/` 用户的家目录模板
- `/var/spool/mail` 目录中存了用户的mail文件

## 2. 用户管理命令

1. 添加用户`useradd 用户名`
	- 参数
		- -g  组名（指定初组）
		- -G  组名（指定附加组，把用户加入组，使用附加组）
		- -c  说明（为用户添加说明信息）
		- -d  目录名（手动指定家目录，家目录不需要建立在`/home/`）
		- -s  指令解析器（手动指定用户登录时使用的指令解析器`shell`）
		- 用户默认值配置文件`/etc/default/useradd`和`/etc/login.defs`

2. 设置密码`passwd 用户名`
	- 参数
		- -S  查询用户的密码状态，仅root用户可用
		- -l  临时锁定用户，仅root用户可用
		- -u  临时解锁用户，仅root用户可用

3. 删除用户`userdel 用户名`
	- 参数
		- -r 连带目录用户的家目录一同删除

4. 添加用户组`groupadd 用户组名`
	- 参数
		- -g GID  (修改ID)
		- -n 新组名 （修改组名，新组名在前，原组名在后）

5. 删除用户组`groupdel 用户组名`
	- 注意：删除组的时候，组当中不能有初始用户

6. 将已存在的用户加入组
	- `gpasswd -a 用户名 组名`  将用户加入组
	- `gpasswd -d 用户名 组名`  将用户从组中移除

7. 切换用户`su 用户名`
	- 连带环境变量一同切换加一个横杠`-`
		- 例如：`su - maqf` 切换用户的使用，同时使用maqf的专属环境变量
	- 只有使用root用户切换到其他用户的时候不需要使用密码
	- 附加：**`sudo`命令，需要修改`/etc/sudoers`配置文件，添加想要扩展权限的用户名**

8. 修改用户信息`usermod 用户名`
	- 参数
		- -u  修改用户UID
		- -c  修改用户的说明信息
		- -G  修改用户的附加组
		- -L  临时锁定用户
		- -U  临时解锁用户

9. 修改密码状态`chage 用户名`
	- 参数
		- -l  （列出用户的详细信息密码状态）
		- -d 日期 （修改密码最后一次更改日期）
		- -m 天数  （修改两次密码修改间隔时间）
		- -M 天数  （密码有效期）
		- -W 天数  （密码过期前警告天数）


## 3. ACL权限

> 针对用户身份不够用的权限解决方案

### 1) 查看分区是否支持ACL

- `df -h`  查看文件系统
- `dumpe2fs -h 分区`  查看分区信息
- `mount -o remount,acl /`  重新挂载分区并添加ACL支持
- `vi /etc/fstab`  修改文件，永久生效

### 2) 设定ACL权限

`setfacl [选项] 文件名`

- 参数
	- -m  设定ACL权限
	- -x  删除指定的ACL权限
	- -b 删除所有的ACL权限
	- -d  设定默认ACL权限
	- -k  删除默认ACL权限
	- -R  递归设定ACL权限（跟在权限后面）

### 3) 查看ACL权限

`getfacl 文件名`

```
# example:
useradd user1
useradd user2
useradd userTemp
# 以上为创建三个用户
groupadd dev  # 创建dev用户组
mkdir /project  # 创建project目录
chown root.dev /project  # 修改project文件所属组
chmod 770 /project  # 修改project目录访问权限
setfacl -m u:userTemp:rx /project  # 为用户设置ACL权限
# 其中u表示为用户设定、g为组设定、m为mask设定
```


# 九、Linux防火墙

## 1. 五个规则链

1. PREROUTING - 路由前
2. INPUT - 数据包流入口
3. FORWARD - 转发管卡
4. OUTPUT - 数据包流出口
5. POSTROUTING - 路由后

## 2. 工作功能和处理方式

常用的功能有以下三种：

- Filter：定义允许或不允许
	- 对于filter来讲一般只能在3个链上：INPUT / FORWARD / OUTPUT
- Nat：定义地址转换
	- 对于nat来讲一般也只能在三个链上：PREROUTING / OUTPUT / POSTROUTING
- Mangle：修改报文元数据
	- Mangle则是5个链都可以做

## 3. 规则的写法

`iptables [-t table] COMMAND [chain] CRETIRIA -j ACTION`

- `-t table`：3个filter nat mangle
- `COMMAND`：定义如何对规则进行管理
- `chain`：指定接下来的规则到底是哪个链上的操作，定义策略时可省略
- `CRETIRIA`：指定匹配标准
- `-j ACTION`：指定如何处理

## 4. 连接管理命令

- `-A`：追加规则
- `-l`：插入规则
- `-P`：设置默认策略（默认开门还是关门）
	- 默认一般只两种（DROP | ACCEPT）
- `-F`：清空规则链（FLASH）

## 5. 查看管理命令

- `-n`：以数字形式查看
- `-v`：查看相关信息（-vv -vvv）
- `--line-number`：显示规则ID号

## 6. 详细匹配标准

通用匹配：源地目标地址的匹配

- `-s`：指定作为原地址匹配，不能使用主机名只能是IP地址
- `-d`：表示目录地址
- `-P：匹配协议（TCP | UDP | ICMP）
- `-i`：指定网卡（eth0 | lo）流入 --- input
- `-o`：指定网卡流出 --- output

扩展匹配：

- `--dport`：指定流入端口（必须同时指定协议）
- `--sport`：指定流出端口（必须同时指定协议）源

## 7. 详解 `-j ACTION`

常见的ACTION

- `DROP`：直接丢弃（不给浏览器任何反馈）
- `REJECT`：明示拒绝
- `ACCEPT`：接受

## 8. 保存防火墙设置

`service iptables save` （不同Liunx版本支持程度不同）
`iptable-save`
`iptable-save > /etc/sysconfig/iptables`

## 9. 案例

```
iptalbes -A INPUT -s 192.168.2.58 -j ACCEPT  # 允许本机ip地址对Linux进行访问
iptables -A INPUT -p tcp --dport 80 -j DROP  # 禁止通过TCP协议对80端口进行访问
```

```shell
#!/bin/bash
#setting iptables

#允许80端口input\output
#默认input/output不允许
#本地操作

IPT=/sbin/iptables

$IPT -F

$IPT -A INPUT -p tcp --dport 22 -j ACCEPT
$IPT -A INPUT -p tcp --dport 80 -j ACCEPT
$IPT -P INPUT DROP

$IPT -A OUTPUT -p tcp --dport 22 -j ACCEPT
$IPT -A OUTPUT -p tcp --dport 80 -j ACCEPT
$IPT -P OUTPUT DROP

$IPT-save > /etc/sysconfig/iptables
```


































































