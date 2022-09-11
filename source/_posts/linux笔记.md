---
title: linux笔记
date: 2020-09-30 09:14:01
tags: 
  - linux
---

linux常用命令
<!--more-->

# linux的运行级别

0：关机
1：单用户
2：多用户
3：命令行模式
4：官方预留，没有使用
5：图形化模式
6：重启

# 运行级别的切换

init 数字

# # linux的目录结构

常见目录：要背下来
1.家目录 （只有自己能进去） ：/root /home/{username}
2.普通用户可执行文件： /bin /usr/bin /usr/local/bin
3.系统管理员可执行的文件： /sbin /usr/sbin /usr/loacal/sbin
(s 表示super)
4.配置文件目录：/etc
5.临时文件目录：/tmp
6.内核和启动文件： /boot
7.服务器数据： /var /srv
8.系统信息：/proc /sys
9.共享库： /lib /usr/lib /usr/local/lib
10.其他挂载点：/media /mnt

注意：文件或者目录名<=255个字符，区分大小写，不能使用/，
不允许创建和系统已有相同名字的目录

# 与目录相关的命令

## 查看当前所在位置的完整路径

```bash
pwd
```

# 创建目录的命令

mkdir 目录名

# 一次性创建多级目录的参数 

-p

# 切换目录的命令

 cd {path}
 绝对路径：以“/”开头的路径都叫绝对路径
 相对路径：只要不是绝对路径就是相对路径
 ~：表示用户的家目录
 /：系统的根目录


# #  显示目录或文件的信息
	语法： ls [选项][参数]
    选项：
	a：查看所有的目录和文件，包含隐藏的
	R：表示递归的显示所有的内容
	l：以详细信息来显示文件、文件夹的内容
	h：自动识别文件的体积单位，必须和l选项一起使用
# #  拷贝功能
	语法： cp {path}/dir|file {path}/dir|file
	选项： r：表示递归处理，用于文件夹的拷贝
	案例：拷贝root目录下的所有内容到/tmp目录下
	cp -r /root/* /tmp
先查看tmp下有啥东西，再查看下root有啥，拷贝再查看
ls
# #  案例：拷贝某个目录下的多个文件（并非全部）
将root下的某2个文件拷贝到home目录下
cp /root/{f1,f2,...} /home/
# #  移动目录
 语法：mv {path}/file1  {path}/file2
 案例：将root目录下的某个文件 移动到root桌面下
 mv /root/f1 /root/桌面
 
 案例：移动文件的同时将其改名
 mv /root/f1  /home/a.txt
 
 # #  创建空白文件
 touch filename
 创建空白文件
 vim a.txt
 
 # #  删除文件、文件夹的内容
 rm -rf file|dir
 删除文件夹必须加r递归处理
 f：强制删除，没有确认删除的提示
 -rf不能随便用，关键文件不能删除


 # #  删除目录
 rmdir dir
 只能删除空的目录
 
# #  批量改名 rename
 touch foo{1..299}
 出来的名字 foo1 foo2 foo11 foo233 数字位数不统一
 
 改名
 rename  原来的字符串 新的字符串（规则）
 rename foo foo0 foo?
 
# 查看运行级别
 who -r
runlevel

# 修改默认的运行级别
vim /etc/inittab中最后的id字段

# 单用户模式
单用户模式可以做密码的修改
进入单用户模式SSH断开，无法提供网络连接，是一种安全模式


1.重启系统
2.鼠标点进去linux操作系统，按下方向键
3.按e进入界面
4.在单用户模式按b
5.通过passwd命令直接重置root密码

# 用户 用户组  其他人
用户
用户组


# 用户 ：增加 删除 修改

用户信息存储的位置：
1.账户信息存储于：/etc/passwd
2.密码信息存储于：/etc/shadow
3.用户组信息存储于：/etc/group

# 增加用户
useradd [选项] {username}
选项：
-c：给账户一个描述信息
-d：给用户指定家目录（只有自己能进去），如果目录不存在，可以配合-m一起使用
-g：为用户指定用户的主组
-G：为用户指定用户的附加组
-s：shell为用户指定shell程序，用户登陆系统之后，此程序就会启动
-u：uid，为用户指定用户的uid

id {username} 查看某个用户的账户信息
# 用户id
root账号 0 超级用户
系统账号 1-499 系统内建，预留
自定义账户 500-65535 程序员手动创建
useradd tom
useradd命令添加完以后应当用root用户执行
passwd {username}
设置完密码后方可进入

# 创建用户tom2，为其指定UID 600,家目录在/root/tom2 
 指定附加组是 tedu  tarena


使用tom2登陆，发现登陆不上
错误信息：./root/tom2/.bash_profile:权限不够
因此设定普通用户的家目录不能设定在root目录下

#  为用户指定一个shell脚本
useradd -s /bin/sh tom3

#  伪用户
作用在于启动、维护系统中的某些程序、进程而去设计的一个概念
此类用户不允许登陆，当程序结束时，该类用户自动退出


# 修改
语法：usermod [选项] {username}
-d 家目录
-g 用户组
-G 覆盖附加组
-aG 追加附加组

修改名字 usermod -l newname oldname
usermod -l tom33 tom3

修改tom4的家目录为/home/tedu
usermod -d /home/tedu tom4

# 删除
语法：userdel [选项]
选项 -r：删除用户的同时删除该用户所有的相关信息

# 密码
root：
passwd 修改root的密码

passwd [选项] {username}
选项：
-l 表示锁定密码，用SSH登陆不上
-u 表示解锁
-d 删除密码

passwd -l tom
passwd -u tom 
passwd -d tom 
删除了某个用户的密码时，该用户无法使用SSH登陆
# passwd 后面不跟用户名则表示修改当前账户的密码
# passwd {username}只有root账户才能修改他人的密码

用户身份的切换
su username 不切换当前目录更换用户
su - username 更换用户切换当前目录到新用户的家目录

# 用户组
# 创建：
  语法：groupadd [选项] groupname
  查看： /etc/group
  
  选项：
  -g 指定组GID
  -o 可以让新的用户组和用户中已经有的组的GID相同，会被系统识别为同一个组
  
# 修改
  语法 groupmod [选项] groupname
选项：
	-g：组id
	-o：修改和已有组一样的id
	-n：用来修改组的名字 groupmod -n newname oldname
	
# 删除 groupdel name


# 用户组的切换



# 权限
逻辑权限
通常一个文件/文件夹 都会用三组权限，每个组都有三个权限
三组分别为：
		用户
		用户组
		其他人
没ui组的标识表示如下： r w x
		读
		写 
		执行

查看文件的权限标识ll
总共有10位：
	第一位：标识文件的类型 文件夹 d 文件-
	第2-4：用户标识，读写执行，如果对应位置没有权限，则使用-表示
	5-7：用户组标识
	8-10：其他人标识

# 修改文件或者文件夹的权限
		chmod [选项] 参数
		选项：-R：表示递归的处理所有的文件
		

# 权限的标识方式。一种是使用字符，另一种是使用数字
r 读 4
w 写 2
x 执行 1
告诉你权限为741： rwxr----x
用户：7 读写执行 
用户组：4 读 
其他人：1 执行


# 修改权限
u:user g:group o:other
加权限
chmod u+x,g+x,o+x dirname
三个都加x
chmod +x dirname
chmod a+x dirname 
减权限
chmod -x dirname

# 物理权限
将某个文件（或目录）给一个修饰权限，这种权限过于执着，
无视用户或者内核级别的操作

rm -rf dir某个文件 不允许任何人删除
	chattr :可以锁定某个文件不能被修改，从而提高文件的安全性
	但是不能保护 / /dev /tmp /var 目录
# 选项
	s:保密性删除文件或目录
	S：及时更新文件或目录（直接写入硬盘）
	u：预防意外删除（删除时，除了加标识，还会加保护区，实在没有空间才会删除）
	R:递归的加权限
	a:表示只能末尾追加内容 用 echo "内容字符串" >> 目标文件
	i:不接受任何修改
	+：表示开启权限
	-：表示关闭某个权限

# lsattr[选项] 参数 查看 
	-R : 递归的显示目录下所有目录的物理权限
	-a：显示所有文件的属性信息
	-d： 显示目录的属性，而不是文件的属性
文件夹 mkdir

创建一个文件 touch

# 普通用户的root权限
有时候普通用户往往会用到root权限，
配置网卡，配置防火墙都需要root权限，

给普通用户root权限

sudo命令 （super User do）
通过修改/etc/sudoers文件，使普通用户可以执行sudo命令，该命令代表着root执行
添加一行 (ALL)=ALL
再su 普通用户

## 挂载概念
	文件系统要能够连接到目录树才能被我们使用。将文件系统和目录书结合的动作称为挂载。
	文件系统：操作系统中负责管理和存储文件信息的目录树

	挂载点：一定是目录（文件夹），此目录为文件系统的入口

	cd-rom，光盘只读存储器
	
	语法：
	mount 设备文件 挂载点
	umount 取消挂载
	首先创建一个目录 /home/cdrom_dir

	/dev/sr0光驱的设备名
	/dev/cdrom 光驱

	linux下的设备都是以文件的形式类管理的
	设备文件的位置 /dev/...
	硬盘的形式 /dev/sda1 /dev/hda1

	前2位sd hd：设备类型
		sd：串口硬盘 
		hd：并行硬盘
		SCSI：速度更快
	第三位：该类型设备的顺序号abc
	sda主盘 sdb从盘
	第四位：该硬盘的分区号1-4为主分区或者拓展分区  5之后逻辑分区
	sd1 sd2 sdb1
	

	# fdisk -l：列出所有的分区，包括没有挂载的
	df -h 查看文件磁盘整体使用情况
		a:列出所有的文件系统
		k：以kb为容量格式显示
		m：mb
		h：以GB MB KB格式显示
		i：以node数量来显示
	
	每个文件系统都有独立的inode block superblock
	inode：记录文件的属性，一个文件占用一个inode，同时记录文件数据所在的block号
	block：块 实际记录文件的内容，若文件太大，会占用多个block
	superblock：记录文件系统的整体信息
	
	#lsblk查看
	
	du：文件系统的磁盘使用量或目录使用量
		-a：列出所有
		-h：易读的容量格式
		-k：kb格式
		-m：MB格式

## 网络
	TCP: 可靠传输协议   安全   不及时  图片、消息
	UDP： 不可靠传输协议  不安全 及时  视频通话



	交换机：组建局域网（内网）

	早期时候，没有交换机，当时使用hub设备进行数据的发送
	hub设备：广播
	A-B
	A-所有人CD
	不安全

	交换机工作原理：通电之后，在自己内部建立一张mac地址表（通过网线连在自己身上的）
		（MAC地址全球唯一）
	一对一发送

	路由器：将局域网终的数据发送到公共网络上（外网）
	 实现网络代理功能，在公共网络上，上网的节点是路由器，并不是电脑
	
	早期，上网的方式通过电话线，拨号上网
	缺点：1，电话一直占线 2，有可能电话进来，网就掉线了
	之后，出现猫（调制解调器），将电话线一分为二，
	缺点，只能一台电脑上网

	所有的设备---交换机  ---路由器--外网

	无线路由器
	

---

#Shell
	shell是操作系统的最外层，shell可以结合编程语言以控制进程和文件，以及启动和控制其他软件。
	shell可以通过提示用户输入信息，向操作系统解析该输入，
	然后处理来自操作系统的结果。
	shell是用户和linux操作系统之间的桥梁。

## linux shell种类
	常见的
	1.Bourne Again Shell(/bin/bash)
	2.Bourne shell(/usr/bin/sh 或者/bin/sh)
	3.C shell
	4.K shell
	5.shell for root

	最常用的是Bourne Again Shell，易用、免费
	日常工作广泛使用
	也是大多数linux系统默认的Bourne Again Shell。

	shell脚本一般文件名以.sh结尾，文件的第一行定义该脚本为shell脚本.

	#shell脚本：
	第一二行为
	#!/bin/bash
	#this is my first shell
	
## 变量(SHELL是一个非类型的解释型语言)
	shell：不用声明类型直接赋值 NUM=19  NAME="TOM"
	
	变量分类：1.局部变量（只在创建他的shell脚本用）
			 2.环境变量
	案例：var1.sh
	变量使用时：$变量名

##shell常见的系统变量
	$0 当前程序的名称
	$n 当前程序的第n个参数，n=1,2,...,9,...，运行时带上参数即可
	$* 当前程序的所有参数（不包括程序本身）
	$# 当前程序的参数个数（不包括程序本身）
	$? 命令或者程序执行完成后的状态
	$UID 获取当前用户的id
	$PWD 当前所在的目录

##输出格式
	echo -e '\033[字背景颜色;字体颜色m显示的字符串\033[0m'
	字的背景颜色有

|颜色|数字|
|---|---|
|黑色|40|
|深红|41|
|绿色|42|
|白色|47|

	字的颜色
|颜色|数字|
|---|---|
|黑|30|
|红|31|
|绿|32|

	特殊效果
|字符|特殊效果|
|---|---|
|4m|下划线|
|5m|闪烁|

##案例:编写一个选择菜单
vim select.sh

##条件语句
	语法1：
		if(表达式);then
			语句1
		fi
	语法2：
		if(表达式);then
			block1
		else
			block2
		f1
	语法3：
		if(表达式);then 
			block1
		elif(表达式);then
			block2
		else
			block3
		fi
案例1：定义一个变量，大于4 的时候输出某个东西
vim if1.sh
##逻辑运算符
	-f 判断文件是否存在 if [ -f filename ]
	判断文件是否不存在 if [ ! -f filename]
	-d 判断目录是否存在 if [ -d dirname ]
	-eq 等于 整数比较 	
	-ne 不等于
	-lt 小于
	-gt 大于
	-le 小于等于
	-ge 大于等于
	-a 双方都成立，相当于C语系中的&&
	-o 单方成立，相当于||
	-z 空字符串
案例2：测试目录是否存在，如果不存在则创建出来，如果存在则提示已经存在
案例3；测试文件是否存在
案例4：判断成绩的等级


## MySQL的使用和备份
	mysql -u root -p
	show databases; 查看存在那些数据库
	create database dxq;创建一个名为dxq的数据库
	use dxq;使用某个数据库
	show tabels;查看该数据库有哪些表格
## 备份逻辑
 1.只有root用户有备份权限，所以先检查是否是root登陆，如果不是则退出
 2.将数据库备份到一个文件夹，所以首先得判断该文件夹是否存在，如果不存在，则先创建出来
 3.备份
 4.检测是否备份成功

`` 反引号中的以命令解析，以命令所得字符串为返回
vim auto_axq_mysql.sh

# 打包、解压缩
	打包：将多个文件打包在一起
	压缩、解压缩：文件体积变化

## 压缩命令
	gzip:拥有更快的压缩性能
	bzip2:拥有更高的压缩比
	compress:几乎不用

	gzip	： 
		压缩：gzip 文件
		解压：gzip -d 文件
	cat：查看文件中的内容 
	zcat：查看gzip算法压缩的文件的内容
	bzcat：查看bzip2算法压缩的文件的内容

## 打包、解包命令
	tar：可以将文件、文件夹打包成一个文件；可以结合gzip、bzip2的算法
		对打包的文件进行相应的压缩和解压
	语法：
		压缩：tar [选项] newFileName.tar.gz sourceFileName
		解压：tar [选项] fileName.tar.gz [-C /path]
	选项：
	-zcvf gzip打包并压缩
	-zxvf gzip解压解包
	-jcvf bzip2打包并压缩
	-jxvf bzip2解压解包
		-c：建立打包文件
		-z：通过gzip算法进行压缩，文件一般以.tar.gz为后缀
		-j:通过bzip2算法进行压缩解压，文件一般以.tar.bz2
		-v：在压缩或解压的过程中。将正在处理的文件名显示出来
		-f：后面跟处理后的文件的全名称（路径+文件名+后缀）
		-C：用在解压时，后面跟解压的路径
		-x:解压的功能

# 软件管理 --RPM RedHat package manger
	优点：
	缺点：依赖关系不会自动安装
	默认路径：
	/etc:一些配置文件的放置的目录
	/usr/bin：可执行文件
	/usr/lib：依赖的程序的动态链接库
	/usr/share/doc：一些基本的软件使用手册和说明文件
	/usr/share/man：一些man page（帮助说明）

	安装说明：
		1.先下载.rpm的文件
		2.将安装包放在某个目录下
		3.安装rpm -ivh packagename.rpm（相对或绝对路径，也可以一下子安装多个）	
		-i：表示安装
		-v：表示处理过程
		-h：显示处理的进度（进度条）
	安装网络上的rpm包
	rpm -ivh "http://url/packagename.rpm"

	卸载：rpm -e packagename(packagename需要通过qa查询)
	|grep regix 管道过滤，在任意多行查询命令中，使用grep过滤出某些需要的条目
## yum命令
	由来：由rpm缺点所致
	yum解决依赖关系
	安装
		redis（非关系型数据库）
		yum install redis
		yum install jdk
		yum install mysql
	卸载
		yum remove 软件名
		yum update 软件名
	

## 网络知识
	通过主机名查看ip 
	例：host www.baidu.com
	www.baidu.com is an alias for www.a.shifen.com.
	www.a.shifen.com has address 183.232.231.174
	www.a.shifen.com has address 183.232.231.172
	www.baidu.com映射到www.a.shifen.com
	www.a.shifen.com映射到真正的ip地址
	
## 远程拷贝协议
	scp:secure copy protocol安全拷贝协议

	scp：必须知道对方的账户和密码，且具备对应权限
	语法: scp [-r] [path]/file|dir  {userName}@Host_ip:/[path]
	-r:传输文件夹
	scp  a.txt root@43.24.52.32:/home

## 私钥文件登陆
	ssh-keygen 输入保存目录，密码等选项，进入生成文件夹
	1.id_rsa 生成的私钥
	2.id_rsa.pub 生成的公钥
	3.authorized_keys 本机已经注册的公钥
	远程注册某个公钥：
	ssh-copy-id -i 公钥.pub文件路径 userName@ip_address
	设置~/.ssh/config文件可以指定一些基本配置，如果不设置，那么默认全部用~/.ssh/下的私钥

## 下载文件
	wget：
	-c 断点续传
	-b 后台下载
	--limit-rate=300k
	-0 重命名

## find命令
	find

## shell脚本的循环语句
	1.for循环
	for((i=0;i<10;i++))
	do
		循环体
	done


	for i in `seq 0 10`
	do
		循环体
	done
案例：从1累加到100 
vim f2.sh
案例：批量解压某目录下的所有的压缩文件 
vim f01.sh
案例：远程主机批量传输文件
	机房有200台电脑
	192.168.1.2
	.....
	192.168.1.202
	把主机上的文件传输给200台电脑
涉及的命令
	scp 文件 root@ip:/home/....
vim f02.sh

	2.while循环
	while 条件语句
	do
		循环体
	done
	
案例：使用while循环从0打印到9
vim w1

## shell脚本执行时从控制台获取数据
	read input
	$input
案例：将本机文件传到list.txt中的所有主机中
vim sp1.sh
vim f11.sh

## until循环
	当满足util时退出循环
	until 条件
	do
		循环体
	done

## switch--case语法
	case 参数 in
		case1)
			语句
			;;
		case2)
			语句
			;;
		*)
			默认语句
			;;
	esac
## select语句
	PS3="which do you want to choice:" #PS3为提示符
	select i in a b c d
	do
	done
案例：vim se1.sh

## linux常用脚本四命令
	sed 命令
	awk 命令
	find 命令
	grep 命令
## sed命令
	vim/vi:采用交互式文本编辑模式，可以使用键盘命令来交互式的插入、删除、修改文本。
	sed采用流编辑模式，最明显的特点：需要预先提供一系列规则，sed会按照规则文档（正则表达式）
	
	（1）每次读取一行内容
	（2）根据提供的规则去修改文件，但是，sed默认不会直接修改源文件，而是修改缓冲区中的数据
 	（3）正则表达式（基础正则表达式+拓展正则表达式）
	 基本：
	 ^ 表示以什么开头  ^a：以a开头
	 $ 是以什么结尾    $b:以b结尾
	 ^$代表空行

	 .代表任意一个字符（不包括空行）
	 *重复前面任意0个或多个字符
	 .*匹配所有字符（包括空行）

	 [abc]匹配括号中的任意一个字符
	 [a-z]匹配任意一个小写字母
	 [A-Z]
	 [0-9]
	 [0-9a-zA-Z]

	 [^abc]不包括abc
	 {n,m} 代表左边的规则出现n-m次 [0-9]{1,3}：0-9的数字出现1到3次
	 {n} 代表左边的规则出现n次

	 拓展正则：
	 + 重复一个或一个以上前面的数据  k+ : kk  kkkk
	 ? 重复0个或1个
	 |多个条件

	多个命令管道 |
	将左边的数据给右边的命令处理

a：新增，在后面
i：插入 在本行的前一行
p：打印
s：取代

n：仅显示处理后的结果
	例：sed 's/addr:/info/g' 命令
	将前一个管道字符串中的addr: 替换为 info
# grep命令：在文本中查找指定的字符串
	类似windows中的ctrl+f，例： grep -v "^$" 查找非空行
	grep常用选项：
		-i:忽略大小写
		-n:显示结果所在的行号
		-c:统计匹配的行数
		-v:反向匹配，符合匹配规则的某些行不显示
		-Ax:在输出的时候，包含结果所在行的后x行 A：after
		-Bx：在输出的时候，包含结果所在行的前x行 B：before
		-Cx：在输出的时候，包含结果所在行的前后x行 C：content
		-E：使用拓展正则表达式[0-9]{1,3} dxq|192
			grep -E 相当于 egrep
	
# awk 强大的文本分析工具，把文件逐行的读入，然后默认以空格将每行进行分割，切开的部分还可以进行各种分析处理
	awk '{print $5}'    打印这一行用空格分开的第5个参数
	awk -F: '{print $2}'   打印这一行用冒号分开的第2个参数
# sort命令
	将结果按行排序

# find命令
	find 要查找的路径
	-maxdepth n:查找的最深路径
	-name "n.txt":查找的文件名
	-type:设置查找的内容为文件f或者目录
	-mtime:设置搜索n天前创建的内容（+n），或者搜索距离现在正好n天创建的文件（n），或者搜索n天之内创建的文件（-n）
	-exec:对查询到的文件进一步操作
	-size:后面加查找的文件的大小，大于n兆（+nM),等于nk（nk），小于n兆（-nM）

	例：find . -maxdepth 3 -mtime +2 -type f -name "*.txt"
	查找当前目录下拓展名为txt，创建时间在2天以前的文件的路径，最大深度为3
	find -exec参数
	find . -name "test.txt" -exec rm -rf {} \; 
	删除查找出来的test.txt文件
	{}:代表find查找出来的所有文件
	\：代表转义

# 自动禁止恶意ip访问
	/etc/sysconfig/iptables：在该文件中把恶意访问的ip地址加入防火墙
	/var/log/secure:在该文件中查找恶意访问的ip有哪些
# 硬件信息收集
	统计信息： ip地址 主机名 cpu 硬盘容量
				内存大小  负载情况
				（1）建表
				（2）统计以上信息插入数据库
				（3）csv逗号分隔表
				事例：sys_collect.sh
# 磁盘报警脚本
	1.怎么统计磁盘容量使用情况
	2.达到85%，则发邮件进行报警
	3.怎么发送邮件
	4.多个报警设置（查看每个磁盘容量的使用情况）
	5.怎么把报警信息（包括磁盘使用情况）写入邮件内容文本中
	发邮件：非QQ邮箱 ，可以使用新浪、163。。。
	mail命令
	-c "抄送地址"
	-b "密送抄送"
	-F "发件人姓名"
	-f "发件人地址"
	echo "assd12138"|mail -s "warning" assd12138@sina.com 
	
#  全量和增量备份linux系统脚本

	在linux系统中，我们经常需要备份系统重要文件，比如/etc，/boot等
	如果每天全备，用时长，网络资源占用大
	进行增量备份

# 怎么进行全量和增量备份

	tar打包备份
	tar -g snapshot -zcf backup_full.tar.gz test
	-g ：跟随时间戳文件，在第一次备份的时候会生成时间戳文件，里面包含了要备份的所有文件的一些时间戳，
	下次再备份的时候，tar命令会利用时间戳文件去比较，只会把这段时间修改的内容进行备份
	例：vim bk.sh  备份的文件夹路径放在参数中

# 使用winscp将




