# DOCKER 基本

## 基本概念



### 目录结构

`docker`镜像放在  `/var/lib/docker`

​	Docker中的镜像采用分层构建设计，每个层可以称之为“layer”，这些layer被存放在了/var/lib/docker/<storage-driver>/目录下，这里的storage-driver可以有很多种如:AUFS、OverlayFS、VFS、Brtfs等。可以通过docker info命令查看存储驱动，（笔者系统是centos7.4）：

目录结构，在不同的操作系统下文件结构不完全一致

| 路径       |      | 含义                                                         |
| ---------- | ---- | ------------------------------------------------------------ |
| containers |      | 用于存储容器信息                                             |
| image      |      | 用来存储镜像中间件及本身信息，大小，依赖信息                 |
| network    |      |                                                              |
| overlay    |      | [docker后端文件存储系统storage-driver(centos7以上)](https://www.cnblogs.com/wdliu/p/10483252.html) |
| plugins    |      |                                                              |
| swarm      |      |                                                              |
| tmp        |      | docker临时目录                                               |
| trust      |      | docker信任目录                                               |
| volumes    |      | docker卷目录                                                 |
| aufs       |      | docker后端文件存储系统storage-driver(ubuntu)                 |
| aufs       | diff | 存放docker image的subimage，每个目录中存放了subimage的真实文件和目录 |

**PS:** 

​		**Docker**最开始支持的文件系统是**Aufs**,是一种**Union File System**,原理就是将多个目录挂在到同一个虚拟目录下，整个文件系统是一个分层的概念。

​		然后**RedHat**系列的系统是不支持**Aufs**的，当Docker变得越来越流行的时候，RedHat公司发行我也得插一脚进去，然后RedHat公司就对这个Aufs研究了一下，然后说“恩，我们牛逼，我要开发一套新的文件系统用来运行Docker”。后来这套系统就是基于自家的在**kernel2.6之后被引进的DeviceMapper技术**。主要用到Docker上的就是**Snapshot**和**Thinly-provisioned Snapshot**, 这个Snapshot在LVM逻辑卷管理场景下用来创建虚拟快照的，Thin-Provisioning是一项利用虚拟化方法减少物理存储部署的技术，可最大限度提升存储空间利用率。当这两个技术结合起来就是DeviceMapper给RedHat系统实现Docker文件系统的最后方案了。其实这个方案也是基于分层的理论给每一层镜像创建快照。然而DeviceMapper的性能并不是那么的好，DC/OS官网给出的解释是会出现unknown issue并且不能再Docker里面运行Docker，DeviceMapper默认情况下创建loop-lvm的方式来构建镜像和容器的snapshots。但是在生产环境下Docker官方建议采用直连的lvm卷来构建镜像和容器，然后在启动Docker Daemon的时候使用如下方式来加载：

```json
{
     "storage-driver": "devicemapper",
     "storage-opts": [         "dm.thinpooldev=/dev/mapper/docker-thinpool",         "dm.use_deferred_removal=true"
     ]
 }
```

​	在系统里运行`docker info`即可查看到使用的是什么文件系统：

这是Cent6.5系统的，发现使用的是devicemapper，而且Data File是loop0

```bash
# docker info
Containers: 11
Images: 63
Storage Driver: devicemapper
 Pool Name: docker-252:1-679218-pool
 Pool Blocksize: 65.54 kB
 Backing Filesystem: extfs
 Data file: /dev/loop0
 Metadata file: /dev/loop1
 Data Space Used: 2.221 GB
 Data Space Total: 107.4 GB
 Data Space Available: 9.648 GB
 Metadata Space Used: 4.166 MB
 Metadata Space Total: 2.147 GB
 Metadata Space Available: 2.143 GB
 Udev Sync Supported: true
 Deferred Removal Enabled: false
 Data loop file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
 Library Version: 1.02.95-RHEL6 (2015-09-08)
Execution Driver: native-0.2
Logging Driver: json-file
Kernel Version: 2.6.32-573.22.1.el6.x86_64
Operating System: <unknown>
CPUs: 2
Total Memory: 2.819 GiB
Name: bastion.shanker
ID: SJVK:KAXO:ZDAB:XWSM:O45I:EF6U:GE2T:RU3Y:NW6B:K4IQ:DYEN:B4BJ
```

Ubuntu12.04的,使用的Aufs系统：

```bash
# docker info
Containers: 1
 Running: 0
 Paused: 0
 Stopped: 1
Images: 38
Server Version: 1.10.3
Storage Driver: aufs
 Root Dir: /var/lib/docker/aufs
 Backing Filesystem: extfs
 Dirs: 101
 Dirperm1 Supported: false
Execution Driver: native-0.2
Logging Driver: json-file
Plugins: 
 Volume: local
 Network: host bridge null
Kernel Version: 3.13.0-24-generic
Operating System: Ubuntu 14.04.1 LTS
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 1.463 GiB
Name: dbslave.shanker
ID: IDR4:IA4B:3GDE:O6KF:IACI:BQE2:5SJL:25CA:4KV3:OCIG:RGYC:N22G
Username: shanker
Registry: https://index.docker.io/v1/
WARNING: No swap limit support
```

CentOS7.2系统，使用的是我OverlayFS系统：

```bash
# docker info
Containers: 3
 Running: 0
 Paused: 0
 Stopped: 3
Images: 5
Server Version: 1.11.1
Storage Driver: overlay
 Backing Filesystem: xfs
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins: 
 Volume: local
 Network: bridge null host
Kernel Version: 3.10.0-327.18.2.el7.x86_64
Operating System: CentOS Linux 7 (Core)
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 3.703 GiB
Name: ukcent2.novalocal
ID: 7Q46:GGPP:HOLP:5ICX:WXV7:ZC73:S45I:HVC2:UEWX:FL6L:DMSC:TVYH
Docker Root Dir: /var/lib/docker
Debug mode (client): false
Debug mode (server): false
Registry: https://index.docker.io/v1/
```

参考：

https://docs.docker.com/engine/userguide/storagedriver/selectadriver/

https://docs.docker.com/engine/userguide/storagedriver/aufs-driver/

https://dcos.io/docs/1.7/administration/installing/custom/system-requirements/#docker

http://coolshell.cn/articles/17200.html

http://www.infoq.com/cn/articles/analysis-of-docker-file-system-aufs-and-devicemapper



## 安装docker

1. 准备

   - 将Docker源添加到系统里

   ```bash
   cat >/etc/yum.repos.d/docker.repo<<E
   [dockerrepo]
   name=Docker Repository
   baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
   enabled=1
   gpgcheck=1
   gpgkey=https://yum.dockerproject.org/gpg
   ```

   

   - 启动

     配置Docker Daemon用OverlayFS启动

   ```bash
   [root@docker ~]# vi /etc/systemd/system/docker.service.d/override.conf 
   [Service]
   ExecStart=
   ExecStart=/usr/bin/docker daemon --storage-driver=overlay
   ```

   

2. 安装Docker，设置开机自启动

   ```bash
   sudo yum -y install docker-engine
   sudo sysctemctl start docker
   sudo systemctl enable docker
   ```

3. 启动

   启动Docker daemon

   ```bash
   [root@docker ~]# systemctl daemon-reload
   [root@docker ~]# service docker start   
   Redirecting to /bin/systemctl start docker.service
   ```

   

4. 查看

   ```bash
   # Docker info
   Storage Driver: overlay
   ```

   

5. 

## docker安装镜像

1. 直接从仓库里拉有建好的image

   ​	

   ```bash
   docker pull imagename
   ```

   拉取回来的

   有时候我们会遇到镜像下载不下来的情况，需要修改仓库地址为国内的镜像地址，比如阿里，清华，中科大等等

   #### 修改仓库地址

   ```bash
   vim /etc/docker/daemon.json
   ```

   ```yaml
   {
     "registry-mirrors" : [
       "http://ovfftd6p.mirror.aliyuncs.com",
       "http://registry.docker-cn.com",
       "http://docker.mirrors.ustc.edu.cn",
       "http://hub-mirror.c.163.com"
     ],
     "insecure-registries" : [
       "registry.docker-cn.com",
       "docker.mirrors.ustc.edu.cn"
     ],
     "live-restore": true,
     "debug" : true,
     "experimental" : true
   }
   ```

   

2. 自己建立image

## 镜像导出



## docker运行管理镜像

## docker 常用命令

