# 远程调试

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