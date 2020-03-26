# docker command

## 如何查看docker 里某个容器的的启动命令

1. 在容器外部，物理机上，可以用`docker inspect`查看或者，`docker inspect container`。

2. 如果在容器内部。可以用&nbsp;`ps -fe`&nbsp;查看。其中1号进程就是启动命令。

3. Docker会在隔离的容器中运行进程。当运行`docker run`命令时，Docker会启动一个进程，并为这个进程分配其独占的文件系统、网络资源和以此进程为根进程的进程组。在容器启动时，镜像可能已经定义了要运行的二进制文件、暴露的网络端口等，但是用户可以通过`docker run`命令重新定义（译者注：`docker run`可以控制一个容器运行时的行为，它可以覆盖`docker build`在构建镜像时的一些默认配置），这也是为什么run命令相比于其它命令有如此多的参数的原因。

4. 命令格式

5. 最基本的docker run命令的格式如下：`$ sudo docker run [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG...]`

6. 如果需要查看`[OPTIONS]`的详细使用说明，请参考Docker关于OPTIONS的章节。这里仅简要介绍Run所使用到的参数。OPTIONS总起来说可以分为两类：
   - 设置运行方式：决定容器的运行方式，前台执行还是后台执行；
   - 设置containerID；
   - 设置网络参数；
   - 设置容器的CPU和内存参数；
   - 设置权限和LXC参数；
   - 设置镜像的默认资源，也就是说用户可以使用该命令来覆盖在镜像构建时的一些默认配置。

7. docker run [OPTIONS]可以让用户完全控制容器的生命周期，并允许用户覆盖执行docker build时所设定的参数，甚至也可以修改本身由Docker所控制的内核级参数。