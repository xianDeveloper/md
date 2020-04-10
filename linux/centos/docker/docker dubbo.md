# dubbo docker

## 编辑镜像

### 建立Dockerfile

```bash
FROM openjdk:8-jdk-alpine
ENV JAVA_OPTS="-Xms1024m -Xmx3072m -XX:PermSize=1024M -XX:MaxPermSize=3072M"
ENV LANG=C.UTF-8
LABEL email="cnhis@cnhis.com"
LABEL name="cnhis-dubbo"
EXPOSE 26884
ENTRYPOINT exec java $JAVA_OPTS -jar /home/cnhis/insurance/insurance.jar
```

### 构建镜像

```bash
docker build -t insurance-dubbo:1.0.0 .
```

## 启动容器

### 启动

```bash
docker run -d --name insurance-dubbo --privileged=true -p 26884:26884 -v /home/cnhis/insurance:/home/cnhis/insurance ab1723738
# --privileged=true 为了解决 在docker中无运行jar包权限
```















​	docker-compose.yml

```yaml
version: '2'
services:
  zk_server: 
    image: daocloud.io/library/zookeeper:3.3.6
    restart: always
  dubbo_admin: 
    image: bolingcavalry/dubbo_admin_tomcat:0.0.1
    links: 
      - zk_server:zkhost
    depends_on:
          - "zk_server"
    ports: 
      - "8080:8080"
    restart: always
  dubbo_provider: 
    image: bolingcavalry/dubbo_provider_tomcat:0.0.1
    links: 
      - zk_server:zkhost
    depends_on:
          - "dubbo_admin"
    environment:
      TOMCAT_SERVER_ID: dubbo_provider_tomcat
    restart: always
  dubbo_consumer: 
    image: bolingcavalry/online_deploy_tomcat:0.0.1
    ports: 
      - "8082:8080"
    environment:
      TOMCAT_SERVER_ID: dubbo_consumer_tomcat
    restart: always
```



在 docker-compose.yml 所在的目录下执行命令`docker-compose up -d`，启动yml文件中定义的四个容器