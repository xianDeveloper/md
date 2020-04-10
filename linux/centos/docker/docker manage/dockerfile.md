# Dockerfile

## Dockerfile介绍

### mysql-Dockerfile

```bash
FROM debian:stretch-slim  # 指定基础镜像，比如 FROM ubuntu:14.04

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
# RUN ：在镜像内部执行一些命令，比如安装软件，配置环境等，换行可以使用""
RUN groupadd -r mysql && useradd -r -g mysql mysql 

RUN apt-get update && apt-get install -y --no-install-recommends gnupg dirmngr && rm -rf /var/lib/apt/lists/*

# add gosu for easy step-down from root
# ENV ：设置变量的值，ENV MYSQL_MAJOR 5.7，可以通过docker run --e key=value修改，后面可以直接使 用${MYSQL_MAJOR}
ENV GOSU_VERSION 1.7
RUN set -x \
    && apt-get update && apt-get install -y --no-install-recommends ca-certificates wget && rm -rf /var/lib/apt/lists/* \
    && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$(dpkg --print-architecture)" \
    && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$(dpkg --print-architecture).asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
    && gpgconf --kill all \
    && rm -rf "$GNUPGHOME" /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true \
    && apt-get purge -y --auto-remove ca-certificates wget

RUN mkdir /docker-entrypoint-initdb.d

RUN apt-get update && apt-get install -y --no-install-recommends \
# for MYSQL_RANDOM_ROOT_PASSWORD
        pwgen \
# for mysql_ssl_rsa_setup
        openssl \
# FATAL ERROR: please install the following Perl modules before executing /usr/local/mysql/scripts/mysql_install_db:
# File::Basename
# File::Copy
# Sys::Hostname
# Data::Dumper
        perl \
    && rm -rf /var/lib/apt/lists/*

RUN set -ex; \
# gpg: key 5072E1F5: public key "MySQL Release Engineering <mysql-build@oss.oracle.com>" imported
    key='A4A9406876FCBD3C456770C88C718D3B5072E1F5'; \
    export GNUPGHOME="$(mktemp -d)"; \
    gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
    gpg --batch --export "$key" > /etc/apt/trusted.gpg.d/mysql.gpg; \
    gpgconf --kill all; \
    rm -rf "$GNUPGHOME"; \
    apt-key list > /dev/null

ENV MYSQL_MAJOR 5.7
ENV MYSQL_VERSION 5.7.28-1debian9

RUN echo "deb http://repo.mysql.com/apt/debian/ stretch mysql-${MYSQL_MAJOR}" > /etc/apt/sources.list.d/mysql.list

# the "/var/lib/mysql" stuff here is because the mysql-server postinst doesn't have an explicit way to disable the mysql_install_db codepath besides having a database already "configured" (ie, stuff in /var/lib/mysql/mysql)
# also, we set debconf keys to make APT a little quieter
RUN { \
        echo mysql-community-server mysql-community-server/data-dir select ''; \
        echo mysql-community-server mysql-community-server/root-pass password ''; \
        echo mysql-community-server mysql-community-server/re-root-pass password ''; \
        echo mysql-community-server mysql-community-server/remove-test-db select false; \
    } | debconf-set-selections \
    && apt-get update && apt-get install -y mysql-server="${MYSQL_VERSION}" && rm -rf /var/lib/apt/lists/* \
    && rm -rf /var/lib/mysql && mkdir -p /var/lib/mysql /var/run/mysqld \
    && chown -R mysql:mysql /var/lib/mysql /var/run/mysqld \
# ensure that /var/run/mysqld (used for socket and lock files) is writable regardless of the UID our mysqld instance ends up having at runtime
    && chmod 777 /var/run/mysqld \
# comment out a few problematic configuration values
    && find /etc/mysql/ -name '*.cnf' -print0 \
        | xargs -0 grep -lZE '^(bind-address|log)' \
        | xargs -rt -0 sed -Ei 's/^(bind-address|log)/#&/' \
# don't reverse lookup hostnames, they are usually another container
    && echo '[mysqld]\nskip-host-cache\nskip-name-resolve' > /etc/mysql/conf.d/docker.cnf
# LABEL : 设置镜像标签 
# LABEL email="itcrazy2016@163.com"
# LABEL name="itcrazy2016"
# VOLUME : 指定数据的挂在目录
VOLUME /var/lib/mysql
# COPY : 将主机的文件复制到镜像内，如果目录不存在，会自动创建所需要的目录，注意只是复制，不会提取和解压
COPY docker-entrypoint.sh /usr/local/bin/
# ADD : 将主机的文件复制到镜像内，和COPY类似，只是ADD会对压缩文件提取和解压
# ADD application.yml /etc/itcrazy2016/

# WORKDIR : 指定镜像的工作目录，之后的命令都是基于此目录工作，若不存在则创建会在/usr/local/tomcat下创建test.txt文件会在/root/test下多出一个app.yml文件
# WORKDIR /usr/local
# WORKDIR tomcat
#RUN touch test.txt # 会在/usr/local/tomcat下创建test.txt文件
RUN ln -s usr/local/bin/docker-entrypoint.sh /entrypoint.sh # backwards compat
# ENTRYPOINT :和CMD的使用类似 .和CMD的不同 docker run执行时，会覆盖CMD的命令，而ENTRYPOINT不会
ENTRYPOINT ["docker-entrypoint.sh"]
# EXPOSE : 指定镜像要暴露的端口，启动镜像时，可以使用-p将该端口映射给宿主机
EXPOSE 3306 33060
# CMD :容器启动的时候默认会执行的命令，若有多个CMD命令，则最后一个生效
CMD ["mysqld"]
```

