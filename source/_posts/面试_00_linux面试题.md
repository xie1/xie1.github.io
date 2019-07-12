---
title: 00_linux面试题
date: 2019-01-14 18:11:20
tags:
categories: Java面试
---
#*面试点*
####1、如何看当前Linux系统有几颗物理CPU和每颗CPU的核数？
	答：
	[root@centos6 ~ 10:55 #35]# cat /proc/cpuinfo|grep -c 'physical id'
	4
	[root@centos6 ~ 10:56 #36]# cat /proc/cpuinfo|grep -c 'processor'
	4

####2、查看系统负载有两个常用的命令，是哪两个？这三个数值表示什么含义呢？

	答：
	[root@centos6 ~ 10:56 #37]# w
	10:57:38 up 14 min,  1 user,  load average: 0.00, 0.00, 0.00
	USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
	root     pts/0    192.168.147.1    18:44    0.00s  0.10s  0.00s w
	[root@centos6 ~ 10:57 #38]# uptime
	10:57:47 up 14 min,  1 user,  load average: 0.00, 0.00, 0.00
	其中load average即系统负载，三个数值分别表示一分钟、五分钟、十五分钟内系统的平均负载，即平均任务数。

####3、vmstat r, b, si, so, bi, bo 这几列表示什么含义呢？
	答：
	[root@centos6 ~ 10:57 #39]# vmstat
	procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
	r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
	0  0      0 1783964  13172 106056    0    0    29     7   15   11  0  0 99  0  0
	r即running，表示正在跑的任务数
	b即blocked，表示被阻塞的任务数
	si表示有多少数据从交换分区读入内存
	so表示有多少数据从内存写入交换分区
	bi表示有多少数据从磁盘读入内存
	bo表示有多少数据从内存写入磁盘
	简记：
	
	i --input，进入内存
	o --output，从内存出去
	s --swap，交换分区
	b --block，块设备，磁盘
	单位都是KB

####4、linux系统里，您知道buffer和cache如何区分吗？

	答：
	buffer和cache都是内存中的一块区域，当CPU需要写数据到磁盘时，由于磁盘速度比较慢，
	所以CPU先把数据存进buffer，然后CPU去执行其他任务，buffer中的数据会定期写入磁盘；
	当CPU需要从磁盘读入数据时，由于磁盘速度比较慢，可以把即将用到的数据提前存入cache，
	CPU直接从Cache中拿数据要快的多。

####5、使用top查看系统资源占用情况时，哪一列表示内存占用呢？

	答：
	PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
	301 root      20   0     0    0    0 S  0.3  0.0   0:00.08 jbd2/sda3-8
	1 root      20   0  2900 1428 1216 S  0.0  0.1   0:01.28 init
	2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd
	3 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 migration/0
	VIRT虚拟内存用量
	RES物理内存用量
	SHR共享内存用量
	%MEM内存用量

####6、如何实时查看网卡流量为多少？如何查看历史网卡流量？

	答：
	安装sysstat包，使用sar命令查看。
	
	yum install -y sysstat#安装sysstat包，获得sar命令
	sar -n DEV#查看网卡流量，默认10分钟更新一次
	sar -n DEV 1 10#一秒显示一次，一共显示10次
	sar -n DEV -f /var/log/sa/sa22#查看指定日期的流量日志
####7、如何查看当前系统都有哪些进程？

	答：
	ps -aux 或者ps -elf
	[root@centos6 ~ 13:20 #56]# ps -aux
	Warning: bad syntax, perhaps a bogus '-'? See /usr/share/doc/procps-3.2.8/FAQ
	USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
	root         1  0.0  0.0   2900  1428 ?        Ss   10:43   0:01 /sbin/init
	root         2  0.0  0.0      0     0 ?        S    10:43   0:00 [kthreadd]
	root         3  0.0  0.0      0     0 ?        S    10:43   0:00 [migration/0]
	root         4  0.0  0.0      0     0 ?        S    10:43   0:00 [ksoftirqd/0]
	……
	[root@centos6 ~ 13:21 #57]# ps -elf
	F S UID        PID  PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
	4 S root         1     0  0  80   0 -   725 -      10:43 ?        00:00:01 /sbin/init
	1 S root         2     0  0  80   0 -     0 -      10:43 ?        00:00:00 [kthreadd]
	1 S root         3     2  0 -40   - -     0 -      10:43 ?        00:00:00 [migration/0]
	1 S root         4     2  0  80   0 -     0 -      10:43 ?        00:00:00 [ksoftirqd/0]
	1 S root         5     2  0 -40   - -     0 -      10:43 ?        00:00:00 [migration/0]
####8、ps 查看系统进程时，有一列为STAT， 如果当前进程的stat为Ss 表示什么含义？如果为Z表示什么含义？

	答：S表示正在休眠；s表示主进程；Z表示僵尸进程。

####9、如何查看系统都开启了哪些端口？

	答：
	[root@centos6 ~ 13:20 #55]# netstat -lnp
	Active Internet connections (only servers)
	Proto Recv-Q Send-Q Local Address               Foreign Address             State       PID/Program name
	tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN      1035/sshd
	tcp        0      0 :::22                       :::*                        LISTEN      1035/sshd
	udp        0      0 0.0.0.0:68                  0.0.0.0:*                               931/dhclient
	Active UNIX domain sockets (only servers)
	Proto RefCnt Flags       Type       State         I-Node PID/Program name    Path
	unix  2      [ ACC ]     STREAM     LISTENING     6825   1/init              @/com/ubuntu/upstart
	unix  2      [ ACC ]     STREAM     LISTENING     8429   1003/dbus-daemon    /var/run/dbus/system_bus_socket
####10、如何查看网络连接状况？

	答：
	[root@centos6 ~ 13:22 #58]# netstat -an
	Active Internet connections (servers and established)
	Proto Recv-Q Send-Q Local Address               Foreign Address             State
	tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN
	tcp        0      0 192.168.147.130:22          192.168.147.1:23893         ESTABLISHED
	tcp        0      0 :::22                       :::*                        LISTEN
	udp        0      0 0.0.0.0:68                  0.0.0.0:*
	……
####11、想修改ip，需要编辑哪个配置文件，修改完配置文件后，如何重启网卡，使配置生效？

	答：使用vi或者vim编辑器编辑网卡配置文件/etc/sysconfig/network-scripts/ifcft-eth0（如果是eth1文件名为ifcft-eth1），内容如下：
	DEVICE=eth0
	HWADDR=00:0C:29:06:37:BA
	TYPE=Ethernet
	UUID=0eea1820-1fe8-4a80-a6f0-39b3d314f8da
	ONBOOT=yes
	NM_CONTROLLED=yes
	BOOTPROTO=static
	IPADDR=192.168.147.130
	NETMASK=255.255.255.0
	GATEWAY=192.168.147.2
	DNS1=192.168.147.2
	DNS2=8.8.8.8
	修改网卡后，可以使用命令重启网卡：
	
	ifdown eth0
	ifup eth0
	也可以重启网络服务：
	
	service network restart
####12、能否给一个网卡配置多个IP? 如果能，怎么配置？

	答：可以给一个网卡配置多个IP，配置步骤如下：
	cat /etc/sysconfig/network-scripts/ifcfg-eth0#查看eth0的配置
	DEVICE=eth0
	HWADDR=00:0C:29:06:37:BA
	TYPE=Ethernet
	UUID=0eea1820-1fe8-4a80-a6f0-39b3d314f8da
	ONBOOT=yes
	NM_CONTROLLED=yes
	BOOTPROTO=static
	IPADDR=192.168.147.130
	NETMASK=255.255.255.0
	GATEWAY=192.168.147.2
	DNS1=192.168.147.2
	DNS2=8.8.8.8
	（1）新建一个ifcfg-eth0:1文件
	
	cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth0:1
	（2）修改其内容如下：
	
	vim /etc/sysconfig/network-scripts/ifcfg-eth0:1
	DEVICE=eth0:1
	HWADDR=00:0C:29:06:37:BA
	TYPE=Ethernet
	UUID=0eea1820-1fe8-4a80-a6f0-39b3d314f8da
	ONBOOT=yes
	NM_CONTROLLED=yes
	BOOTPROTO=static
	IPADDR=192.168.147.133
	NETMASK=255.255.255.0
	GATEWAY=192.168.147.2
	DNS1=192.168.147.2
	DNS2=8.8.8.8
	（3）重启网络服务：
	
	service network restart
####13、如何查看某个网卡是否连接着交换机？

	答：mii-tool eth0 或者 mii-tool eth1

####14、如何查看当前主机的主机名，如何修改主机名？要想重启后依旧生效，需要修改哪个配 置文件呢？

	答：查看主机名：
	hostname
	centos6.5
	修改主机名：
	hostname centos6.5-1
	永久生效需要修改配置文件：
	
	vim /etc/sysconfig/network
	NETWORKING=yes
	HOSTNAME=centos6.5-1
####15、设置DNS需要修改哪个配置文件？

	答：
	（1）在文件 /etc/resolv.conf 中设置DNS
	（2）在文件 /etc/sysconfig/network-scripts/ifcfg-eth0 中设置DNS

####16、使用iptables 写一条规则：把来源IP为192.168.1.101访问本机80端口的包直接拒绝

	答：iptables -I INPUT -s 192.168.1.101 -p tcp --dport 80 -j REJECT

####17、要想把iptable的规则保存到一个文件中如何做？如何恢复？
	答：使用iptables-save重定向到文件中：
	iptables-save > 1.ipt
	使用iptables-restore反重定向回来：

	iptables-restore < 1.ipt
####18、如何备份某个用户的任务计划？

	答：将/var/spool/cron/目录下指定用户的任务计划拷贝到备份目录cron_bak/下即可
	cp /var/spool/cron/rachy /tmp/bak/cron_bak/

####19、任务计划格式中，前面5个数字分表表示什么含义？

	答：依次表示：分、时、日、月、周

####20、如何可以把系统中不用的服务关掉？

	答：（1）使用可视化工具：ntsysv
	（2）使用命令：
	chkconfig servicename off

####21、如何让某个服务（假如服务名为 nginx）只在3,5两个运行级别开启，其他级别关闭？

	答：先关闭所有运行级别：
	chkconfig nginx off
	然后打开35运行级别：
	chkconfig --level 35 nginx on

####22、rsync 同步命令中，下面两种方式有什么不同呢？

	(1) rsync -av  /dira/  ip:/dirb/
	(2) rsync -av  /dira/  ip::dirb
	答：(1)前者是通过ssh方式同步的
	(2)后者是通过rsync服务的方式同步的

####23、rsync 同步时，如果要同步的源中有软连接，如何把软连接的目标文件或者目录同步？

	答：同步源文件需要加-L选项

####24、某个账号登陆linux后，系统会在哪些日志文件中记录相关信息？

	答：用户身份验证过程记录在/var/log/secure中，登录成功的信息记录在/var/log/wtmp。

####25、网卡或者硬盘有问题时，我们可以通过使用哪个命令查看相关信息？

	答：使用命令dmesg

####26、分别使用xargs和exec实现这样的需求，把当前目录下所有后缀名为.txt的文件的权限修改为777

	答：
	（1）find ./ -type f -name "*.txt" |xargs chmod 777
	（2）find ./ -type f -name "*.txt" -exec chmod 777 {} ;

####27、有一个脚本运行时间可能超过2天，如何做才能使其不间断的运行，而且还可以随时观察脚本运行时的输出信息？

	答：使用screen工具

####28、在Linux系统下如何按照下面要求抓包：只过滤出访问http服务的，目标ip为192.168.0.111，一共抓1000个包，并且保存到1.cap文件中？

	答：tcpdump -nn -s0 host 192.168.0.111 and port 80 -c 1000 -w 1.cap

####29、rsync 同步数据时，如何过滤出所有.txt的文件不同步？

	答：加上--exclude选项：
	
	--exclude=“*.txt”
####30、rsync同步数据时，如果目标文件比源文件还新，则忽略该文件，如何做？

	答：保留更新使用-u或者--update选项

####31、想在Linux命令行下访问某个网站，并且该网站域名还没有解析，如何做？

	答：在/etc/hosts文件中增加一条从该网站域名到其IP的解析记录即可，或者使用curl -x

####32、自定义解析域名的时候，我们可以编辑哪个文件？是否可以一个ip对应多个域名？是否一个域名对应多个ip？

	答：编辑 /etc/hosts ,可以一个ip对应多个域名，不可以一个域名对多个ip

####33、我们可以使用哪个命令查看系统的历史负载（比如说两天前的）？

	答：sar -q -f /var/log/sa/sa22  #查看22号的系统负载

####34、在Linux下如何指定dns服务器，来解析某个域名？

	答：使用dig命令：dig @DNSip http://domain.com
	
	如：dig @8.8.8.8 www.baidu.com#使用谷歌DNS解析百度
####35、使用rsync同步数据时，假如我们采用的是ssh方式，并且目标机器的sshd端口并不是默认的22端口，那我们如何做？

	答：rsync "--rsh=ssh -p 10022"或者rsync -e "ssh -p 10022"

####36、rsync同步时，如何删除目标数据多出来的数据，即源上不存在，但目标却存在的文件或者目录？

	答：加上--delete选项

####37、使用free查看内存使用情况时，哪个数值表示真正可用的内存量？

	答：free列第二行的值

####38、有一天你突然发现公司网站访问速度变的很慢很慢，你该怎么办呢？

	（服务器可以登陆，提示：你可以从系统负载和网卡流量入手）
	
	答：可以从两个方面入手分析：分析系统负载，使用w命令或者uptime命令查看系统负载，
	如果负载很高，则使用top命令查看CPU，MEM等占用情况，要么是CPU繁忙，要么是内存不够，
	如果这二者都正常，再去使用sar命令分析网卡流量，分析是不是遭到了攻击。
	一旦分析出问题的原因，采取对应的措施解决，如决定要不要杀死一些进程，或者禁止一些访问等。

####39、rsync使用服务模式时，如果我们指定了一个密码文件，那么这个密码文件的权限应该设置成多少才可以？

答：600或400



----------



###填空
	1、在Linux系统中，以 文件 方式访问设备。
	2、Linux内核引导时，从文件 /etc/fstab 中读取要加载的文件系统
	3、Linux文件系统中每个文件用 i节点 来标识
	4、全部磁盘块由四个部分组成，分别为: 引导块、专用块、i节点块、数据存储块
	5、前台起动的进程使用: ctrl+c 禁止
	6、安装Linux系统对硬盘分区时，必须有两种分区类型：文件系统 和 交换分区。
	7、网络管理的重要任务是 监控 和 控制
	8、内核分为 文件管理系统、I/O管理系统 、内存管理系统 和进程管理系统 等四个子系统。
###系统
	1、Linux开机启动过程？
	1）主机加电自检，加载BOLS硬件信息
	2）读取MBR的引导文件（grub，lilo）
	3）引导linux内核
	4）运行第一个进程init（进程号永远为1）
	5）进入相应的运行级别
	6）运行终端，输入用户名和密码
	2、Linux系统缺省的运行级别
	0.关机
	1.单机用户模式 
	2.字符界面的多用户模式（不支持网络）
	3.字符界面的多用户模式
	4.未分配使用 
	5.图形界面的多用户模式 
	6.重启
	3、Linux系统是由那些部分组成？
	Linux系统内核，shell，文件系统和应用程序四部分组成
	
	4、硬链接和软链接有什么区别？
	1）硬链接不可以跨分区，软件链可以跨分区
	2）硬链接指向一个i节点，而软链接则是创建一个新的i节点
	3）删除硬链接文件，不会删除原文件，删除软链接文件，会把原文件删除
	5、如何规划一台Linux主机，步骤是怎样？
	1、确定机器是做什么用的，比如是做web、db、还是游戏服务器
	2、确定好之后，就要定系统需要怎么安装，默认安装哪些系统、分区怎么做
	3、需要优化系统的哪些参数，需要创建哪些用户等等的
	6、查看系统当前进程连接数？
	netstat -an | grep ESTABLISHED | wc -l
	7、如何在/usr目录下找出大小超过10MB的文件?
	find /usr -type f -size +10240k
	8、添加一条到192.168.3.0/24的路由，网关为192.168.1.254？
	route add -net 192.168.3.0/24 netmask 255.255.255.0 gw 192.168.1.254
	9、如何在/var目录下找出90天之内未被访问过的文件?
	find /var \! -atime -90
	10、如何在/home目录下找出120天之前被修改过的文件?
	find /home  -mtime +120
	11、在整个目录树下查找文件“core”，如发现则无需提示直接删除它们。
	find / -name core -exec rm {} \;
	12、有一普通用户想在每周日凌晨零点零分定期备份/user/backup到/tmp目录下，该用户应如何做?
	crontab -e
	0 0 * * 7 /bin/cp /user/backup /tmp
	13、每周一下午三点将/tmp/logs目录下面的后缀为*.log的所有文件rsync同步到备份服务器192.168.1.100中同样的目录下面，crontab配置项该如何写：
	00 15 * * 1 rsync -avzP /tmp/logs/*.log root@192.168.1.100:/tmp/logs
	14、找到/tmp/目录下面的所有名称以"_s1.jpg"结尾的普通文件，如果其修改日期在一天内，则将其打包到/tmp/back.tar.gz文件中
	find /tmp -type f -name ".*_sj.jpg" -mtime 1|xarges tar zxf /tmp/back.tar.gz
	15、配置mysql服务器的时候，配置了auto_increment_increment=3，请问这里的3意味着什么？
	auto_increment是用于主键自动增长的，从3开始增长，3表示自增的起始值
	
	16、详细说明keepalived的故障切换工作原理
	这种故障切换是通过VRRP协议来实现的，主节点会按一定的时间间隔发送心跳信息的广播包，
	告诉备节点自己的存活状态信息，当主节点发生故障时，备节点在一段时间内就收到广播包，
	从而判断主节点出现故障，因此会调用自身的接管程序来接管主节点的IP资源及服务，
	当主节点恢复时，备节点会主动释放资源，恢复到接管前的状态，从而来实现主备故障切换

###安全
	1、防火墙有几张表几条链？
	4张表，5条链
	
	2、一台Linux系统初始化环境后需要做一些什么安全工作？
	1、添加普通用户登陆，禁止root用户登陆，更改SSH端口号
	2、服务器使用密钥登陆，禁止密码登陆
	3、开启防火墙，关闭SElinux，根据业务需求设置相应的防火墙规则
	4、装fail2ban这种防止SSH暴力破击的软件
	5、设置只允许公司办公网出口IP能登陆服务器（看公司实际需要）
	6、设置nginx_waf模块防止SQL注入
	7、把Web服务使用www用户启动，更改网站目录的所有者和所属组为www
	8、修改历史命令记录的条数为10条
	3、什么叫CC攻击？什么叫DDOS攻击？怎么预防CC攻击和DDOS攻击？
	简介：
	
	CC攻击主要是用来攻击页面的，模拟多个用户不停的对你的页面进行访问，从而使你的系统资源消耗殆尽
	DDOS攻击中文名叫分布式拒绝服务攻击，指借助服务器技术将多个计算机联合起来作为攻击平台，来对一个或多个目标发动DDOS攻击，
	攻击即是通过大量合法的请求占用大量网络资源，以达到瘫痪网络的目的
	预防：
	
	防CC/DDOS攻击这些只能是用硬件防火墙做流量清洗，将攻击流量引入黑洞
	流量清洗这一块，主要是买ISP服务商的防攻击的服务就可以，机房一般有空余流量，
	我们一般是买服务，毕竟攻击不会是持续长时间
	
	4、什么是网站数据库注入？怎么过滤与预防网站数据库注入？
	简介：
	由于程序员的水平及经验参差不齐，大部分程序员在编写代码的时候，没有对用户输入数据的合法性进行判断，
	应用程序存在安全隐患。用户可以提交一段数据库查询代码，根据程序返回的结果，获得某些他想得知的数据，这就是所谓的SQL注入。
	SQL注入是从正常的WWW端口访问，而且表面看起来跟一般的Web页面访问没什么区别，如果管理员没查看日志的习惯，可能被入侵很长时间都不会发觉。
	过滤与预防：
	数据库网页端注入这种，可以考虑使用nginx_waf做过滤与预防

###网络
	写出如何给apache增加virtualhost，让访问http://www.test.com和http://www.test.cn的时候，都打开/var/www/html目录下面的文件：
	<VirtualHost *:80>
	    ServerAdmin admini@abc.com
	    DocumentRoot "/var/www/html"
	    ServerName www.test.com
	    ServerAlias  test.cn
	    ErrorLog "logs/bbs-error_log"
	    CustomLog "logs/bbs-access_log" common
	</VirtualHost>
	用一条命令显示本机eth0网卡的IP地址，不显示其它字符
	#方法一：
	ifconfig eth0|grep inet|awk -F ':' '{print $2}'|awk '{print $1}'
	#方法二
	ifconfig eth0|grep "inet addr"|awk -F '[ :]+' '{print $4}' 
	#方法三：
	ifconfig eth0|awk -F '[ :]+' 'NR==2 {print $4}' 
	#方法四：
	ifconfig eth0|sed -n '2p'|sed 's#^.*addr:##g'|sed 's# Bc.*$##g'
	#方法五：
	ifconfig eth0|sed -n '2p'|sed -r 's#^.*addr:(.*)  Bc.*$#\1#g'
	#方法六(centos7也适用)：
	ip addr|grep eth0|grep inet|awk '{print $2}'|awk -F '/' '{print $1}'
	写出一个curl命令，访问指定服务器61.135.169.121上的如下URL：http://www.baidu.com/s?wd=test，访问的超时时间是20秒：
	curl --connect-timeout 20 http://61.135.169.121/s?wd=test
	用netstat命令配合其他shell命令，按照源IP统计所有到80端口的ESTABLISHED状态链接的个数，输出结果类似（第一列为连接数，第二列为IP）：
	[root@ ~]# netstat -an|grep ESTABLISHED
	tcp        0     52 139.224.199.85:22           101.47.33.86:51763          ESTABLISHED 
	tcp        0      0 139.224.199.85:45368        106.11.68.13:80             ESTABLISHED 
	[root@ ~]# netstat -an|grep ESTABLISHED|grep ":80"
	tcp        0      0 139.224.199.85:45368        106.11.68.13:80             ESTABLISHED
	netstat -an|grep ESTABLISHED|grep ":80"|awk 'BEGIN{FS="[[:space:]:]+"}{print $4}'
	说明：FS 是字段分隔符,简单的可以用多个awk过滤。
	
	如果需要进行整理并排序的话，完整命令如下
	
	netstat -an|grep ESTABLISHED|grep ":80"|awk 'BEGIN{FS="[[:space:]:]+"}{print $4}'|sort|uniq -c|sort -nr
###shell编程
		用Shell编程，判断一文件是不是字符设备文件，如果是将其拷贝到 /dev 目录下。
		#!/bin/bash
		read -p "Input file name: " FILENAME
		if [ -c "$FILENAME" ];then
		　　cp $FILENAME /dev
		fi
		设计一个shell程序，添加一个新组为class1，然后添加属于这个组的30个用户，用户名的形式为stdxx，其中xx从01到30。
		#!/bin/bash
		groupadd class1
		for((i=1;i<31;i++))
		do
		        if [ $i -le 10 ];then
		                useradd -g class1 std0$i
		        else
		                useradd -g class1 std$i
		        fi
		done
		编写shell程序，实现自动删除50个账号的功能。账号名为stud1至stud50。
		#!/bin/bash
		for((i=1;i<51;i++))
		do
		                userdel -r stud$i
		done
		写一个sed命令，修改/tmp/input.txt文件的内容。
		要求：(1) 删除所有空行；
		(2) 一行中，如果包含"11111"，则在"11111"前面插入"AAA"，在"11111"后面插入"BBB"，
		比如：将内容为0000111112222的一行改为：0000AAA11111BBB2222
		
		[root@~]# cat -n /tmp/input.txt
		     1  000011111222
		     2
		     3  000011111222222
		     4  11111000000222
		     5
		     6
		     7  111111111111122222222222
		     8  2211111111
		     9  112222222
		    10  1122
		    11
		#删除所有空行命令
		[root@~]# sed '/^$/d' /tmp/input.txt
		000011111222
		000011111222222
		11111000000222
		111111111111122222222222
		2211111111
		112222222
		1122
		插入指定的字符
		[root@~]# sed 's#\(11111\)#AAA\1BBB#g' /tmp/input.txt
		0000AAA11111BBB222
		0000AAA11111BBB222222
		AAA11111BBB000000222
		AAA11111BBBAAA11111BBB11122222222222
		22AAA11111BBB111
		112222222
		1122 


#二、*参考链接地址*
[https://zhuanlan.zhihu.com/p/32250942](https://zhuanlan.zhihu.com/p/32250942)
[https://gywbd.github.io/posts/2014/8/50-linux-commands.html](https://gywbd.github.io/posts/2014/8/50-linux-commands.html)
[https://www.leolan.top/index.php/posts/36.html](https://www.leolan.top/index.php/posts/36.html)