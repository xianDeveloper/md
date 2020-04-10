# docker command

## docker image -- container

1. image 类比为 类,container 类比为对象
   我们运行container(对象)中并去修改相应的变量等,一些操作如下:

   - 查询所有的container 

     `docker container ls -a` 

   - 交互运行container (即可进入这个container,进行操作!) 

     `docker run -it centos`  

   - 列出image 

     `docker images` 

   - 列出containers 

     `docker ps -a` 

   - 列出所有container 的id 

     `docker container ls -aq` 

   - 清理 所有container 

     `docker rm $(docker container ls -aq)` 

   - 停container服务 

     `docker container stop $(docker container ls -aq)` 

   - 按条件查看container 

     `docker container ls -f “status=exited”`

## run 还是 cmd 还是entrypoint

1. 概要
   run 执行命令并创建 新的image layer,这是在build的时候去执行，和下面在run时候运行是不一样的!! （yum 安装一些内容)

   cmd 设置容器启动后默认执行的命令和参数
   ENTRYPOINT :设置容器启动时运行的命令

2. shell 和 exec 格式来执行
   这只是一种格式，三者都可以用。

   ```bash
   shell格式
   run apt-get install -y vim
   cmd echo “hello docker”
   ENTRYPOINT echo “hello docker”
   
   exec 格式
   run [“apt-get”,“install”,"-y",“vim”]
   CMD ["/bin/echo",“hello docker”]
   ENTRYPOINT ["/bin/echo",“hello docker”]
   
   FROM centos
   ENV name Docker
   CMD echo “hello $name”
   
   ENTRYPOINT echo “hell $name” //OK
   ENTRYPOINT ["/bin/echo",“hello $name”] //不OK
   ENTRYPOINT ["/bin/bash","-c",echo hello $name"] //OK
   ```

3. CMD和entrypoint的区别

   - CMD的操作:
     容器run时默认执行的命令
     如果docker run 指定了命令,则会被忽略 即 docker run -it [image] /bin/bash输出,则cmd会被忽略!
     定义了多个cmd ,只有最后一个会执行

     以下为测试:

   ```bash
   ****Dockerfile为:****
   FROM centos
   ENV name Docker
   CMD echo "hello $name"
   
   
   ****下面build然后处理****
   
   ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker build -t gminghubdocker/test_cmd .
   Sending build context to Docker daemon  2.048kB
   Step 1/3 : FROM centos
    ---> 1e1148e4cc2c
   Step 2/3 : ENV name Docker
    ---> Running in cedc67b4c0b8
   Removing intermediate container cedc67b4c0b8
    ---> d18fe34d6a8f
   Step 3/3 : CMD echo "hello $name"
    ---> Running in bfe5bb10bd0a
   Removing intermediate container bfe5bb10bd0a
    ---> 4236541e7835
   Successfully built 4236541e7835
   Successfully tagged gminghubdocker/test_cmd:latest
   ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker run gminghubdocker/test_cmd
   hello Docker
   
   ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker run -it gminghubdocker/test_cmd /bin/bash    ****这里自定义一下cmd,则Dockerfile里的cmd会被忽略,所以不会执行hello $name****
   [root@f9f771770748 /]#   ****由于用了-i ，且cmd是/bin/bash ,则这里进了container****
   [root@f9f771770748 /]#
   [root@f9f771770748 /]#
   [root@f9f771770748 /]# exit
   exit
   ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ 
   ```

   - ENTRYPOINT的操作:

     让容器以应用程序 或者服务的形式运行
     不会被忽略 ，一定会执行 （下面的实验发现多个entrypoint 也会只执行最后一个)
     常用在写一个shell脚本作为entrypoint.如:

     ```bash
     COPY abc.sh /usr/local/bin/
     ENTRYPOINT ["abc.sh"]
     ```

     一个测试例子:

     ```bash
     ****Dockerfile为:****
     FROM centos
     ENV name Docker
     CMD echo "hello $name"
     
     
     ****下面build然后处理****
     
     
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker build -t gminghubdocker/test_entrypoint .
     Sending build context to Docker daemon  2.048kB
     Step 1/3 : FROM centos
      ---> 1e1148e4cc2c
     Step 2/3 : ENV name Docker
      ---> Using cache
      ---> d18fe34d6a8f
     Step 3/3 : ENTRYPOINT echo "hello $name"
      ---> Running in 5b310ba43f5d
     Removing intermediate container 5b310ba43f5d
      ---> e2ed341433bb
     Successfully built e2ed341433bb
     Successfully tagged gminghubdocker/test_entrypoint:latest
     
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker run gminghubdocker/test_entrypoint
     hello Docker
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker run -it gminghubdocker/test_entrypoint
     hello Docker
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker run -it gminghubdocker/test_entrypoint /bin/bash
     hello Docker
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$
     ```

     如果是entrypoint 的话, docker run -it [image] /bin/bash也不会进入交互式的环境!!!..

   - 实验多个 cmd与多个entrypoint

     ```bash
     FROM centos
     ENV name Docker
     CMD echo "hello cmd"
     ENTRYPOINT echo "hello $name"
     
     ****执行情况 :****
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker run gminghubdocker/test35
     hello Docker
     
     
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker run -it gminghubdocker/test35 /bin/bash
     hello Docker
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$
     ```

   - 多个entrypoint和cmd:

     ```bash
     FROM centos
     ENV name Docker
     CMD echo "hello cmd"
     CMD echo "hello cmd"
     CMD echo "hello cmd"
     ENTRYPOINT echo "hello e1"
     ENTRYPOINT echo "hello e2"
     ENTRYPOINT echo "hello e3"
     
     
     
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker run gminghubdocker/test35:latest
     hello e3
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$
     
     
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$ docker run -it gminghubdocker/test35:latest /bin/bash
     hello e3
     ming@ming-CW35S:~/dgame/project/docker_pratise/mytest128$
     ```

     

   - 多个entrypoint和run:

     ```bash
     FROM centos
     ENV name Docker
     CMD echo "hello cmd"
     RUN yum install -y screen
     CMD echo "hello cmd"
     CMD echo "hello cmd"
     ENTRYPOINT echo "hello e1"
     RUN yum list zlib
     ENTRYPOINT echo "hello e2"
     ENTRYPOINT echo "hello e3"
     ```

     



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

   