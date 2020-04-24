# tomcat basic

## [tomcat 的最大连接数](https://blog.csdn.net/quliuwuyiz/article/details/79979031)

前提说明

为了确保服务不会被过多的http长连接压垮，我们需要对tomcat设定个最大连接数，超过这个连接数的请求会拒绝，让其负载到其它机器。达到保护自己的同时起到连接数负载均衡的作用。

## [Tomcat connector元素常用配置(最大连接数等)](https://www.cnblogs.com/edison2012/p/4594858.html)

在tomcat的server.xml中有类似:

```xml
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" maxSpareThreads="750" maxThreads="1000"  

 minSpareTHreads="50" acceptCount="1000"  maxProcessors="1000"   URIEncoding="gbk" useBodyEncodingForURI="true"/>  
```

 的配置, 其中:

 ```bash
acceptCount="1000"  #可接受的最大连接数
maxProcessors="1000"  #最大活动线程数
port="8080"   #服务端口
protocol="HTTP/1.1"   #服务协议
connectionTimeout="20000"  #超时时间 单位是ms
redirectPort="8443"  #重定向端口 需要安全通信的场合,将把客户请求转发至SSL的redirectPort端口
maxThreads= #Tomcat可创建的最大的线程数，每一个线程处理一个请求； maxThreads决定了tomcat的最大线程阀值，需要设置的大一些
minSpareThreads= #最小备用线程数，tomcat启动时的初始化的线程数；
maxSpareThreads= #最大备用线程数，一旦创建的线程超过这个值，Tomcat就会关闭不再需要的socket线程；
URIEncoding="gbk"   #设置tomcat默认的转码格式
 ```

查看$TOMCAT_HOME/webapps/tomcat-docs/config/http.html这个说明文档，有如下说明：  

URIEncoding ：This specifies the character encoding used to decode the URI bytes, after %xx decoding the URL. If not specified, ISO-8859-1 will be used. 也就是说，如果没有设置URIEncoding， Tomcat默认是按ISO-8859-1进行URL解码，ISO-8859-1并未包括中文字符，这样的话中文字符肯定就不能被正确解析了。

**一.Tomcat连接池配置**       Tomcat想要承受大的并发量,必须增大连接数,一般的Tomcat的Connector可以做如下修改:       

```xml
<Connector port="80" protocol="HTTP/1.1" 
    connectionTimeout="60000" 
    redirectPort="8443"
    maxThreads="5000" 
    acceptCount="500"
    minSpareThreads="100"
    maxSpareThreads="5000" 
    enableLookups="false"
    compression="on"
    compressionMinSize="2048"
    compressableMimeType="text/html,text/xml,text/javascript,text/css,text/plain"
    disableUploadTimeout="true"
    URIEncoding="UTF-8"
/>
```



其中几个关键的参数: 

```bash
connectionTimeout= #连接超时,毫秒为单位.对于高并发对实时要求不高的可以使适当增加该值 

maxThreads= #最大并发连接数  表示最多同时处理150个连接  

acceptCount= #在超过最大连接数后,可以接受的排队数量  当同时连接的人数达到maxThreads时，还可以接收排队的连接，超过这个连接的则直接返回拒绝连接。

minSpareThreads= #Tomcat初始化时默认创建的线程数,也是以后线程增加时一次增加的最小数量 表示即使没有人使用也开这么多空线程等待

maxSpareThreads= #这个参数标识,一旦创建的线程数量超过这个值,Tomcat就会关闭不活动的线程*   表示如果最多可以空75个线程，例如某时刻有80人访问，之后没有人访问了，则tomcat不会保留80个空线程，而是关闭5个空的。  

enableLookups= #关闭DNS查询  在实现中,我们发现使用该配置,连接数上去之后很难下降,导致CPU一直维持在一个比较高的水平. 

```

后来我们换了一种连接方式,采用线程池的方式,首先定义一个Executor: 

```xml
<Executor name="tomcatThreadPool" 
        namePrefix="tomcatThreadPool-" 
        maxThreads="1000" 
        maxIdleTime="300000"
        minSpareThreads="200"/>
```

参数的意义和上述相同 
在Connector中使用定义的这个连接池: 

```xml
<Connector executor="tomcatThreadPool"
           port="20003" protocol="HTTP/1.1"
           acceptCount="800"
           minProcessors="300"
           maxProcessors = "1000"
           redirectPort="8443" />
```

**minProcessors,maxProcessors 与上面的minSpareThreads,maxThreads 意义差不多.**  使用连接池以后,发现连接数上升后如果一段时间没有请求了,连接数会很快下降,CPU的消耗得到了有效的降低,  处理能力得到了增强.
如何查看当前tomcat的连接数呢?
假设服务器上开启了 2个tomcat实例,分别监听8040和8050端口

```bash
netstat -na | grep ESTAB | grep 8040 | wc -l
netstat -na | grep ESTAB | grep 8050 | wc -l
```

二者之和,就是所有tomcat的连接数

 

