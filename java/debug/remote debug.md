# 远程调试

## tomcat 远程调试

1. 配置tomcat/bin/catalina.sh

   **JPDA_ADDRESS=”5055”**，默认为8000。启动tomcat时，用 **./catalina.sh jpda start** 代替原本的 **./startup.sh** 来启动，然后在Intellij 里面做如下配置：

   ```bash
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5055
   host: 192.168.1.191 port: 5055
   ```
   
   
   
   <img src="C:\Users\54888\Desktop\remote debug1.png" alt="remote debug1" style="zoom:75%;" />
   
   服务端 用  下面命令启动

```bash
 ./bin/catalina.sh jpda start & tail -f ./logs/catalina.out 
```

​		启动idea调式



2. 部署异常 ERROR: Cannot load this JVM TI agent twice

   去掉 catalina.sh 重复的配置 

## springboot 远程调试

- 服务端启动

  ```bash
  java -jar -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8000 operationplatform-0.0.1-SNAPSHOT.war
  ```

- 客户端配置

  ```bash
  -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8000
  ```

  ![image](E:\doc\学习积累\md\java\debug\springboot-idea-debug.png)

## dubbo 远程调试

- 由于Dubbo的特性是远程调用，因此正常来说无法在本地进行debug

  - 因为你调用的方法在别台机器上跑，你只能知道给他的input和他返回的结果，但没办法知道这个接口内部的执行，所以也没办法在裡面打断点进行debug

  - 就算在本地有dubbo代码，在本地的dubbo代码打断点也没办法debug，因为实际上调用的是远程服务器上的dubbo代码，而不是本地的dubbo代码

  - 所以如果要对远程服务器上的dubbo代码进行debug，需要进行特别的设置

- 远程debug

  - 服务器配置

    假设有2台机器，一台机器是本地平常写代码的机器，另一台是服务器，专门运行dubbo

    首先在dubbo运行的服务器上运行以下指令

    - 100.80.169.72 是本地写代码的机器的ip

    - 9097 是指将dubbo服务透过socket转发出来的端口 (9097可以换，只要跟idea的配置一致就可以)

    ```bash
    socat TCP4-LISTEN:9097,fork,range=100.80.169.72/32 TCP4:127.0.0.1:9015在
    
    ```

  - 本地机器上的idea进行配置

    - 新增一个remote连接

    ![](E:\doc\学习积累\md\java\debug\dubbo.png)

    - 将Host设为dubbo服务器的ip，Port设为9097 (只要和TCP-LISTEN的端口一致即可)

    ![dubbo-idea](E:\doc\学习积累\md\java\debug\dubbo-idea.png)

    - 如此就可以在本地的dubbo代码上打断点，这个断点位置会透过socket传到远程dubbo服务器上，因此我们就可以在本地对远程的dubbo代码进行debug

- 

