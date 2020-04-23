# portainer

## Portainer介绍

Portainer是Docker的图形化管理工具，提供状态显示面板、应用模板快速部署、容器镜像网络数据卷的基本操作（包括上传下载镜像，创建容器等操作）、事件日志显示、容器控制台操作、Swarm集群和服务等集中管理和操作、登录用户管理和控制等功能。功能十分全面，基本能满足中小型单位对容器管理的全部需求。

## 下载Portainer镜像

```bash
# 查询当前有哪些Portainer镜像
docker search portainer
# 下载镜像
docker pull docker.io/portainer/portainer
```

## 单机版运行

如果仅有一个`docker`宿主机，则可使用单机版运行，`Portainer`单机版运行十分简单，只需要一条语句即可启动容器，来管理该机器上的docker镜像、容器等数据。

```bash
docker run -d -p 9000:9000 \
    --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    --name prtainer-test \
    docker.io/portainer/portainer
```

