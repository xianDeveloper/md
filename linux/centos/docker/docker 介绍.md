# DOCKER 基本

## 安装docker

## docker安装镜像

### 修改仓库地址

```ba
vim /etc/docker/daemon.json
```

```json
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



## 

## docker运行管理镜像

## docker 常用命令

