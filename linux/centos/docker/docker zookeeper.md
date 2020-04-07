# zookeeper 

##  docker 下的zookeeper常用操作

### 常用命令

1. 首先使用 `docker exec -it zk3 bash`  命令进入服务器
2. 使用命令 `./bin/zkServer.sh status` 来查看节点的状态
3. 使用`zkCli.sh`开启客户端
4. 使用 `create -e /node1 node1.1` 创建临时节点，当客户端关闭时候，该节点会随之删除。不加参数`－e`创建永久节点。
5. `get /node`:获取节点值
6. `ls /node`：列出节点
7.  `delete /node` 删除节点
8. `stat`：查看节点信息
9. `setAct path acl`：用于设置节点访问权限
10. `getAcl path` ：查看节点的权限信息

### ACL

- ACL全称是Access Control List，访问控制列表，zookeeper中ACL由三部分组成，即Scheme:id:permission，其中：    scheme是验证过程中使用的检验策略；id是权限被赋予的对象，比如ip或某个用户；permission为可以操作的权限，有5个值：crdwa，分别表示create、read、delete、write、admin,具体含义在[zookeeper ACL](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fzkp_java%2Farticle%2Fdetails%2F82635101%23t14)中已经描述过。作者：矮肥链接：https://www.jianshu.com/p/9005cba6616b来源：简书著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

- 通过*setAcl path acl*命令可以设置节点的访问权限，path是节点路径，acl是要设置的权限(crdwa)。通过*getAcl path*可以查看节点的权限信息。需要注意的是节点的acl不具有继承关系。
  作者：矮肥链接：https://www.jianshu.com/p/9005cba6616b来源：简书著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### scheme

- 权限检验策略(scheme)有五种类型：world、auth、digest、IP、super.

- 格式：world:anyone:permission
  world:anyone:crdwa标记为所有用户都可以create read delete write 节点。

- auth：    

  - auth:username:password:crd  表示给认证通过的用户设置crd权限。 
  - 可以通过addauth digest <username>:<password>命令添加用户。

- ip

  - ip:id:permission ：表示指定某个地址可以有权限。

- super

  - super检验策略主要供运维人员维护节点使用。

- digest：

  - digest:id:permission   digest格式中的id需要使用sha1进行加密；这个命令的作用和auth差不多。

  

  

  

  

  

  

  

  

  

  

# 