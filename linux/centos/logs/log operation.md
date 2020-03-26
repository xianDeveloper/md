# log操作

## CentOS6下记录后台操作日志的两种方式

### **1、利用script以及scriptreplay工具**

script一般默认已安装，可以使用script工具记录用户在当前终端的所有的操作，已经输出到屏幕的内容。将这些信息保存到指定的文本文件中。 

也就是说，script命令在你需要记录或者存档终端活动时可能很有用，记录文件会存储为文本文件，所以可以很方便地用文本编辑器打开。 

在使用script命令将终端的会话过程录制下来之后，可以使用 scriptreplay将其录制的结果进行回放。 

script 的好处就在于你在终端中的所有操作、敲过的命令和打印出的结果它都可以原原本本地进行录制。

下面介绍如何使用script

开启记录，并输出到文本及时间节点记录文件

```bash
script -t 2> test.time -a test.log
```

回放的话使用

```bash
scriptreplay test.time test.log
```

如果要一登录就自动利用script进行记录，首先创建mkdir -p /var/log/script_log/目录

然后在/etc/profile最后追加如下脚本

```bash
if [ $UID -ge 0 ];then

   exec /usr/bin/script -qaf -t 2> /var/log/script_log/$USER-$UID-`date +%Y%m%d%H%M`.time -a /var/log/script_log/$USER-$UID-`date +%Y%m%d%H%M`.out

fi
```

scriptreplay xxx.time xxx.out文件查看回放录像

### **2、记录history到日志文件的方式**

建脚本文件/etc/log_history.sh脚本如下

```bash
[root@VM_Server ~]# cat log_history.sh 

#!/bin/bash
history
USER_IP=`who -u am i 2>/dev/null| awk '{print $NF}'|sed -e 's/[()]//g'`
if [ "$USER_IP" = "" ]
then
        USER_IP=`hostname`
fi
logname=`whoami`
if [ ! -d /var/log/history_log ] && [ "$logname" == "root" ]
then
        mkdir -p /var/log/history_log
        chmod 777 /var/log/history_log
fi
if [ ! -d /var/log/history_log/${logname} ] && [ "$logname" == "root" ]
then
        mkdir /var/log/history_log/${logname}
        chmod 300 /var/log/history_log/${logname}
fi
export HISTSIZE=4096
DT=`date +"%Y%m%d_%H:%M:%S"`
export HISTFILE="/var/log/history_log/${logname}/${logname}@${USER_IP}_history_$DT"
chmod 600 /var/log/history_log/${logname}/*history* 2>/dev/null
```

追加到/etc/profile下

```bash
echo ". /etc/log_history.sh" >> /etc/profile
```

