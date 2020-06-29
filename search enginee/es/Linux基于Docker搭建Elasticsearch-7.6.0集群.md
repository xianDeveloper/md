# [Linux基于Docker搭建Elasticsearch-7.6.0集群](https://blog.csdn.net/gfk3009/article/details/104560431/)

Linux基于Docker搭建Elasticsearch-7.6.0集群
1、首先我们需要安装Docker
2、下载镜像
3、设置Elasticsearch挂载目录
4、创建多个yml至data目录中
5、开启端口或关闭防火墙
6、建立网络
7、安装并启动容器
8、检测集群是否的搭建成功
9、设置Kibana挂载目录
10、创建Kibana的YML文件
11、安装并启动Kibana容器
12、登录网址进行验证
13、验证是否连接集群
14、错误总结
15、其他命令总结

## 1、首先我们需要安装Docker
Docker安装教程
本文章目前的docker版本：19.03.6

## 2、下载镜像
下载Elasticsearch镜像

```ba
docker pull elasticsearch:7.6.0
```

下载Kibana镜像

```bash
docker pull kibana:7.6.0
```



## 3、设置Elasticsearch挂载目录
设置文件夹
#存放配置文件的文件夹

#存放数据的文件夹
mkdir -p /home/elasticsearch/node-1/data
mkdir -p /home/elasticsearch/node-2/data
mkdir -p /home/elasticsearch/node-3/data
#存放运行日志的文件夹
mkdir -p /home/elasticsearch/node-1/log
mkdir -p /home/elasticsearch/node-2/log
mkdir -p /home/elasticsearch/node-3/log
#存放IK分词插件的文件夹
mkdir -p /home/elasticsearch/node-1/plugins
mkdir -p /home/elasticsearch/node-2/plugins
mkdir -p /home/elasticsearch/node-3/plugins
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
对文件加设置开放权限，如果不开放权限，则会报错无法写入数据的情况。
chmod 777 /home/elasticsearch/node-1/data
chmod 777 /home/elasticsearch/node-2/data
chmod 777 /home/elasticsearch/node-3/data
chmod 777 /home/elasticsearch/node-1/plugins
chmod 777 /home/elasticsearch/node-2/plugins
chmod 777 /home/elasticsearch/node-3/plugins
chmod 777 /home/elasticsearch/node-1/log
chmod 777 /home/elasticsearch/node-2/log
chmod 777 /home/elasticsearch/node-3/log
chmod 777 /home/elasticsearch/node-1/config
chmod 777 /home/elasticsearch/node-3/config
chmod 777 /home/elasticsearch/node-2/config
1
2
3
4
5
6
7
8
9
10
11
12
4、创建多个yml至data目录中
三个配置不同之处为node.name，设置映射端口，设置内部通讯端口，由于我们是放置在docker上部署，所以物理机相同，但是端口需要改变。部署的时候最好将注释的中文以及无用的特殊字符删除，因为有可能会抛出异常
创建node-1的YML文件

#集群名称
cluster.name: my-es
#当前该节点的名称
node.name: node-1
#是不是有资格竞选主节点
node.master: true
#是否存储数据
node.data: true
#最大集群节点数
node.max_local_storage_nodes: 3
#给当前节点自定义属性（可以省略）
#node.attr.rack: r1
#数据存档位置
path.data: /usr/share/elasticsearch/data
#日志存放位置
path.logs: /usr/share/elasticsearch/log
#是否开启时锁定内存（默认为是）
#bootstrap.memory_lock: true
#设置网关地址，我是被这个坑死了，这个地址我原先填写了自己的实际物理IP地址，
#然后启动一直报无效的IP地址，无法注入9300端口，这里只需要填写0.0.0.0
network.host: 0.0.0.0
#设置其它结点和该结点交互的ip地址，如果不设置它会自动判断，值必须是个真实的ip地址，设置当前物理机地址,
#如果是docker安装节点的IP将会是配置的IP而不是docker网管ip
network.publish_host: 192.168.114.136
#设置映射端口
http.port: 9200
#内部节点之间沟通端口
transport.tcp.port: 9300
#集群发现默认值为127.0.0.1:9300,如果要在其他主机上形成包含节点的群集,如果搭建集群则需要填写
#es7.x 之后新增的配置，写入候选主节点的设备地址，在开启服务后可以被选为主节点，也就是说把所有的节点都写上
discovery.seed_hosts: ["192.168.114.136:9300","192.168.114.136:9301","192.168.114.136:9302"]
#当你在搭建集群的时候，选出合格的节点集群，有些人说的太官方了，
#其实就是，让你选择比较好的几个节点，在你节点启动时，在这些节点中选一个做领导者，
#如果你不设置呢，elasticsearch就会自己选举，这里我们把三个节点都写上
cluster.initial_master_nodes: ["node-1","node-2","node-3"]
#在群集完全重新启动后阻止初始恢复，直到启动N个节点
#简单点说在集群启动后，至少复活多少个节点以上，那么这个服务才可以被使用，否则不可以被使用，
gateway.recover_after_nodes: 2
#删除索引是是否需要显示其名称，默认为显示
#action.destructive_requires_name: true

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
创建node-2的YML文件

#集群名称
cluster.name: my-es
#当前该节点的名称
node.name: node-2
#是不是有资格竞选主节点
node.master: true
#是否存储数据
node.data: true
#最大集群节点数
node.max_local_storage_nodes: 3
#给当前节点自定义属性（可以省略）
#node.attr.rack: r1
#数据存档位置
path.data: /usr/share/elasticsearch/data
#日志存放位置
path.logs: /usr/share/elasticsearch/log
#是否开启时锁定内存（默认为是）
#bootstrap.memory_lock: true
#设置网关地址，我是被这个坑死了，这个地址我原先填写了自己的实际物理IP地址，
#然后启动一直报无效的IP地址，无法注入9300端口，这里只需要填写0.0.0.0
network.host: 0.0.0.0
#设置其它结点和该结点交互的ip地址，如果不设置它会自动判断，值必须是个真实的ip地址，设置当前物理机地址,
#如果是docker安装节点的IP将会是配置的IP而不是docker网管ip
network.publish_host: 192.168.114.136
#设置映射端口
http.port: 9201
#内部节点之间沟通端口
transport.tcp.port: 9301
#集群发现默认值为127.0.0.1:9300,如果要在其他主机上形成包含节点的群集,如果搭建集群则需要填写
#es7.x 之后新增的配置，写入候选主节点的设备地址，在开启服务后可以被选为主节点，也就是说把所有的节点都写上
discovery.seed_hosts: ["192.168.114.136:9300","192.168.114.136:9301","192.168.114.136:9302"]
#当你在搭建集群的时候，选出合格的节点集群，有些人说的太官方了，
#其实就是，让你选择比较好的几个节点，在你节点启动时，在这些节点中选一个做领导者，
#如果你不设置呢，elasticsearch就会自己选举，这里我们把三个节点都写上
cluster.initial_master_nodes: ["node-1","node-2","node-3"]
#在群集完全重新启动后阻止初始恢复，直到启动N个节点
#简单点说在集群启动后，至少复活多少个节点以上，那么这个服务才可以被使用，否则不可以被使用，
gateway.recover_after_nodes: 2
#删除索引是是否需要显示其名称，默认为显示
#action.destructive_requires_name: true
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
创建node-3的YML文件

#集群名称
cluster.name: my-es
#当前该节点的名称
node.name: node-3
#是不是有资格竞选主节点
node.master: true
#是否存储数据
node.data: true
#最大集群节点数
node.max_local_storage_nodes: 3
#给当前节点自定义属性（可以省略）
#node.attr.rack: r1
#数据存档位置
path.data: /usr/share/elasticsearch/data
#日志存放位置
path.logs: /usr/share/elasticsearch/log
#是否开启时锁定内存（默认为是）
#bootstrap.memory_lock: true
#设置网关地址，我是被这个坑死了，这个地址我原先填写了自己的实际物理IP地址，
#然后启动一直报无效的IP地址，无法注入9300端口，这里只需要填写0.0.0.0
network.host: 0.0.0.0
#设置其它结点和该结点交互的ip地址，如果不设置它会自动判断，值必须是个真实的ip地址，设置当前物理机地址,
#如果是docker安装节点的IP将会是配置的IP而不是docker网管ip
network.publish_host: 192.168.114.136
#设置映射端口
http.port: 9202
#内部节点之间沟通端口
transport.tcp.port: 9302
#集群发现默认值为127.0.0.1:9300,如果要在其他主机上形成包含节点的群集,如果搭建集群则需要填写
#es7.x 之后新增的配置，写入候选主节点的设备地址，在开启服务后可以被选为主节点，也就是说把所有的节点都写上
discovery.seed_hosts: ["192.168.114.136:9300","192.168.114.136:9301","192.168.114.136:9302"]
#当你在搭建集群的时候，选出合格的节点集群，有些人说的太官方了，
#其实就是，让你选择比较好的几个节点，在你节点启动时，在这些节点中选一个做领导者，
#如果你不设置呢，elasticsearch就会自己选举，这里我们把三个节点都写上
cluster.initial_master_nodes: ["node-1","node-2","node-3"]
#在群集完全重新启动后阻止初始恢复，直到启动N个节点
#简单点说在集群启动后，至少复活多少个节点以上，那么这个服务才可以被使用，否则不可以被使用，
gateway.recover_after_nodes: 2
#删除索引是是否需要显示其名称，默认为显示
#action.destructive_requires_name: true
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
5、开启端口或关闭防火墙
临时关闭防火墙命令
systemctl stop firewalld
1
开启相关端口（推荐）
firewall-cmd --zone=public --add-port=9200/tcp --permanent
firewall-cmd --zone=public --add-port=9201/tcp --permanent
firewall-cmd --zone=public --add-port=9202/tcp --permanent
firewall-cmd --zone=public --add-port=9300/tcp --permanent
firewall-cmd --zone=public --add-port=9301/tcp --permanent
firewall-cmd --zone=public --add-port=9302/tcp --permanent
#这个是kibana端口，等会会用到
firewall-cmd --zone=public --add-port=5601/tcp --permanent
1
2
3
4
5
6
7
8
更新防火墙规则
firewall-cmd --complete-reload
1
查看当前所开放的端口
firewall-cmd --zone=public --list-ports
1
6、建立网络
如果需要安装kibana等其他，需要创建一个网络，名字任意取，让他们在同一个网络，使得elasticsearch和kibana通信，网络名称可以自定义
docker network create es-net
1
7、安装并启动容器
请仔细查看创建容器的命令，network以及挂在目录端口等等，都需要填写正确，如果启动失败请看最下方的错误总结。是否能够帮助到你

安装并启动node-1容器
docker run -e ES_JAVA_OPTS="-Xms512m -Xmx512m" --network es-net -d -p 9200:9200 -p 9300:9300 -v /home/elasticsearch/node-1/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /home/elasticsearch/node-1/plugins:/usr/share/elasticsearch/plugins -v /home/elasticsearch/node-1/data:/usr/share/elasticsearch/data -v /home/elasticsearch/node-1/log:/usr/share/elasticsearch/log --name es-node-1 elasticsearch:7.6.0
1
安装并启动node-2容器
docker run -e ES_JAVA_OPTS="-Xms512m -Xmx512m" --network es-net -d  -p 9201:9201 -p 9301:9301  -v /home/elasticsearch/node-2/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml  -v /home/elasticsearch/node-2/plugins:/usr/share/elasticsearch/plugins   -v /home/elasticsearch/node-2/data:/usr/share/elasticsearch/data -v /home/elasticsearch/node-2/log:/usr/share/elasticsearch/log --name es-node-2 elasticsearch:7.6.0
1
安装并启动node-3容器
docker run -e ES_JAVA_OPTS="-Xms512m -Xmx512m" --network es-net -d -p 9202:9202 -p 9302:9302  -v /home/elasticsearch/node-3/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /home/elasticsearch/node-3/plugins:/usr/share/elasticsearch/plugins -v /home/elasticsearch/node-3/data:/usr/share/elasticsearch/data/ -v /home/elasticsearch/node-3/log:/usr/share/elasticsearch/log --name es-node-3 elasticsearch:7.6.0
1
8、检测集群是否的搭建成功
打开浏览器，键入访问地址与端口，并加上后缀**_cat/nodes?pretty**
http://192.168.114.136:9200/_cat/nodes?pretty
1
出现以下图片即表示搭建成功，带*的为master节点

9、设置Kibana挂载目录
设置文件夹
mkdir -p /home/kibana/config
1
10、创建Kibana的YML文件
创建文件
vim /home/kibana/config/kibana.yml
1
写入文件内容，建意去除中文
#Kibana的映射端口
server.port: 5601

#网关地址
server.host: "0.0.0.0"

#Kibana实例对外展示的名称
server.name: "kibana-192.168.114.136"

#Elasticsearch的集群地址，也就是说所有的集群IP
elasticsearch.hosts: ["http://192.168.114.136:9200","http://192.168.114.136:9201","http://192.168.114.136:9202"]

#设置页面语言，中文使用zh-CN，英文使用en
i18n.locale: "zh-CN"

#这个配置还没理解清楚………………
xpack.monitoring.ui.container.elasticsearch.enabled: true
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
11、安装并启动Kibana容器
输入命令，指定network网络
docker run -d -p 5601:5601 -v /home/kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml --network es-net  --name kibana kibana:7.6.0
1
12、登录网址进行验证
登录Kibana网址
http://192.168.114.136:5601
1

13、验证是否连接集群
在控制台界面输命令，即可看到节点存活树
GET _cat/nodes?v
1


14、错误总结
1、在设置配置文件yml时，误将network.host设置成了本机地址，并且没有设置network.publish_host参数，导致启动一直抛异常，这里我们只需要将network.host的值设置为0.0.0.0，network.publish_host设置为自己的本机地址即可解决问题
错误提示1：BindTransportException[Failed to bind to [9300]]
错误提示2：java.net.BindException: Cannot assign requested address
图片展示

2、在设置挂在目录时，提示权限不足，导致启动失败，可是我挂在目录权限都已经开放了呀，这是什么问题呢？
其实是因为，使用docker创建elasticsearch容器时，就会在我们的挂载目录中写入数据（这里以/home/elasticsearch/node-1/data目录为例），而这些数据文件是无法进行变动的，这个时候我们将dockers容器删除，并且再创建新的容器挂载到相同目录下，就会出现此错误，报错权限不足，这个时候，我们只需要在我们的挂载目录下将原有的数据删除，并重新创建容器即可。

rm -rf /home/elasticsearch/node-1/data/*
rm -rf /home/elasticsearch/node-2/data/*
rm -rf /home/elasticsearch/node-3/data/*
1
2
3
错误内容1：nested: AccessDeniedException[/usr/share/elasticsearch/data/nodes/1]

错误内容2：java.nio.file.AccessDeniedException: /usr/share/elasticsearch/data/nodes/1

图片展示

3、未设置虚拟内存，启动将会报错
出现此错误，是因为我们没有在宿主机上设置虚拟内存，此时我们只需要设置虚拟内存即可启动
错误内容：max virtual memory areas vm.max_map_count [65530] is too low, increase to at least

vim /etc/sysctl.conf
1
在文件下方输入
vm.max_map_count=655360
1
保存退出，并刷新系统配置
sysctl -p /etc/sysctl.conf/
1
4、虚拟机ES无法互相访问
出现此错误是因为docker处于安全考虑默认关闭该设置

错误内容：WARNING: IPv4 forwarding is disabled. Networking will not work

解决方案：

#打开文件
vim /etc/sysctl.conf
#新增内容
net.ipv4.ip_forward=1
#重启network服务
systemctl restart network
#查看是否修改成功，如果返回net.ipv4.ip_forward = 1，即修改成功
sysctl net.ipv4.ip_forward
1
2
3
4
5
6
7
8
15、其他命令总结
检查集群健康情况
GET _cat/health?v
1

status状态说明
1、green: 所有primary shard和replica shard都已成功分配, 集群是100%可用的;
2、yellow: 所有primary shard都已成功分配, 但至少有一个replica shard缺失. 此时集群所有功能都正常使用, 数据不会丢失, 搜索结果依然完整, 但集群的可用性减弱. —— 需要及时处理的警告.
3、red: 至少有一个primary shard(以及它的全部副本分片)缺失 —— 部分数据不能使用, 搜索只能返回部分数据, 而分配到这个分配上的写入请求会返回一个异常. 此时虽然可以运行部分功能, 但为了索引数据的完整性, 需要尽快修复集群.
检查集群中节点的个数
GET _cat/nodes?v
1
检查集群中节点的索引
GET _cat/indices?v
1

————————————————
版权声明：本文为CSDN博主「伊格尼斯」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/gfk3009/article/details/104560431/