# 常见网络相关命令

## [防火墙](https://www.linuxidc.com/Linux/2016-12/138979.htm)

### [CentOS 6.5查看防火墙的状态](http://www.linuxidc.com/topicnews.aspx?tid=14)

```bash
service iptable status  #查看防火墙的状态
servcie iptables stop   #临时关闭防火墙
chkconfig iptables off  #永久关闭防火墙
```



### Centos 7  firewall 命令

```bash
检查防火墙的状态
systemctl status firewalld.service
或者
systemctl list-unit-files|grep firewalld.service

关闭防火墙
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
systemctl start firewalld.service	#启动一个服务 
systemctl stop firewalld.service	#关闭一个服务：
systemctl restart firewalld.service	#重启一个服务：
systemctl status firewalld.service	#显示一个服务的状态：
systemctl enable firewalld.service	#在开机时启用一个服务：
systemctl disable firewalld.service	#在开机时禁用一个服务：
systemctl is-enabled firewalld.service;echo $?	#查看服务是否开机启动：
systemctl list-unit-files|grep enabled	#查看已启动的服务列表：

查看已经开放的端口：
firewall-cmd --list-ports

开启端口
firewall-cmd --zone=public --add-port=9001/tcp --permanent
命令含义：
–zone #作用域
–add-port=80/tcp #添加端口，格式为：端口/通讯协议
–permanent #永久生效，没有此参数重启后失效

重启防火墙
firewall-cmd --reload #重启firewall
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）
```



查看占用端口

lsof -i tcp:5055

Centos查看端口占用情况命令，比如查看80端口占用情况使用如下命令：

lsof -i tcp:80

列出所有端口

netstat -ntlp

1、开启端口（以80端口为例）

​      方法一：

​         /sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT   写入修改

​         /etc/init.d/iptables save   保存修改

​        service iptables restart    重启防火墙，修改生效

​       方法二：

​       vi /etc/sysconfig/iptables  打开配置文件加入如下语句:

​       -A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT   重启防火墙，修改完成

2、关闭端口

​     方法一：

​         /sbin/iptables -I INPUT -p tcp --dport 80 -j DROP   写入修改

​         /etc/init.d/iptables save   保存修改

 	        service iptables restart    重启防火墙，修改生效 

​       方法二：

​       vi /etc/sysconfig/iptables  打开配置文件加入如下语句:

​       -A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j DROP   重启防火墙，修改完成

3、查看端口状态

​      /etc/init.d/iptables status

有时启动应用时会发现端口已经被占用，或者是感觉有些端口自己没有使用却发现是打开的。这时我们希望知道是哪个应用/进程在使用该端口。

 	CentOS下可以用netstat或者lsof查看，Windows下也可以用netstat查看，不过参数会不同 

Linux:

netstat -nap #会列出所有正在使用的端口及关联的进程/应用

lsof -i :portnumber #portnumber要用具体的端口号代替，可以直接列出该端口听使用进程/应用

 	一、检查端口被哪个进程占用 

 	 netstat -lnp|grep 88   #88请换为你的apache需要的端口，如：80 

 	SSH执行以上命令，可以查看到88端口正在被哪个进程使用。如进程号为 1777 。 

 	二、查看进程的详细信息   ps 1777   SSH执行以上命令。查看相应进程号的程序详细路径。 

 	三、杀掉进程，重新启动apache   kill -9 1777        #杀掉编号为1777的进程（请根据实际情况输入）   service httpd start #启动apache 

 	SSH执行以上命令，如果没有问题，apache将可以正常启动。 

   Windows系统:  netstat -nao #会列出端口关联的的进程号，可以通过任务管理器查看是哪个任务  最后一列为程序PID，再通过tasklist命令：tasklist | findstr 2724  再通过任务管理结束掉这个程序就可以了 