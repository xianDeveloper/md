1. # [solr 基础概念](https://www.cnblogs.com/yulonglyw/p/8595144.html)

   ## 什么是solr

   **就是一个web****工程**

   solr本质是一个web服务，我们将它复制到tomcat8下：

   #### Solr和lucene区别

   Lucene是一个开放源代码的全文检索引擎工具包，它不是一个完整的全文检索引擎，Lucene提供了完整的查询引擎和索引引擎，目的是为软件开发人员提供一个简单易用的工具包，以方便的在目标系统中实现全文检索的功能，或者以Lucene为基础构建全文检索引擎。

   Solr的目标是打造一款企业级的搜索引擎系统，它是一个搜索引擎服务，可以独立运行，通过Solr可以非常快速的构建企业的搜索引擎，通过Solr也可以高效的完成站内搜索功能。

   ### 为什么要solr：

   - 1、solr是将整个索引操作功能封装好了的搜索引擎系统(企业级搜索引擎产品)
   - 2、solr可以部署到单独的服务器上(WEB服务)，它可以提供服务，我们的业务系统就只要发送请求，接收响应即可，降低了业务系统的负载
   - 3、solr部署在专门的服务器上，它的索引库就不会受业务系统服务器存储空间的限制
   - 4、solr支持分布式集群，索引服务的容量和能力可以线性扩展

   ### solr的工作机制：

   - 1、solr就是在lucene工具包的基础之上进行了封装，而且是以web服务的形式对外提供索引功能
   - 2、业务系统需要使用到索引的功能（建索引，查索引）时，只要发出http请求，并将返回数据进行解析即可

   Solr 是Apache下的一个顶级开源项目，采用Java开发，它是基于Lucene的全文搜索服务器。Solr提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展，并对索引、搜索性能进行了优化。

   Solr可以独立运行，运行在Jetty、Tomcat等这些Servlet容器中，Solr 索引的实现方法很简单，用 POST 方法向 Solr 服务器发送一个描述 Field 及其内容的 XML 文档，Solr根据xml文档添加、删除、更新索引 。Solr 搜索只需要发送 HTTP GET 请求，然后对 Solr 返回Xml、json等格式的查询结果进行解析，组织页面布局。Solr不提供构建UI的功能，Solr提供了一个管理界面，通过管理界面可以查询Solr的配置和运行情况。

   ## [安装solr](https://www.cnblogs.com/xiaoxiaoyihan/p/11719488.html)

   安装solr，就是去部署它的war包

   文基于以下开源组件版本搭建，**约定下载后组件和解压缩的文件都放置在/opt目录下**：

   - solr-8.2.0
   - apache-tomcat-8.5.47

   首先下载solr-8.2.0.tgz，可以使用wget命令:

   ```shell
   wget http://mirrors.tuna.tsinghua.edu.cn/apache/lucene/solr/8.2.0/solr-8.2.0.tgz
   ```

   解压缩：

   ```shell
   tar -zxvf solr-8.2.0.tgz -C .
   ```

   解压后，/opt目录下会多一个solr-8.2.0目录

   下载apache-tomcat-8.5.47：

   ```shell
   wget https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.47/bin/apache-tomcat-8.5.47.tar.gz
   ```

   解压缩：

   ```shell
   tar -zxvf apache-tomcat-8.5.47.tar.gz
   ```

   为了将solr部署到tomcat服务器，不使用solr自带的jetty，首先在/opt目录下创建一个目录用于部署solr服务，名称无限制，这里取名`solr`了。

   ```shell
   mkdir solr
   ```

   复制一份tomcat到`/opt/solr`目录下，重命名为tomcat8

   ```shell
   cp -r apache-tomcat-8.5.47 solr/tomcat8
   ```

   solr本质是一个web服务，我们将它复制到tomcat8下：

   ```shell
   cp -r solr-8.2.0/server/solr-webapp/webapp solr/tomcat8/webapps/solr
   ```

   复制solr-8.2.0/server/lib/ext下的部分jar到solr目录中，为了简便可以完全复制所有的，然后忽略掉disruptor-3.4.2.jar

   ```shell
   cp solr-8.2.0/server/lib/ext/* solr/tomcat8/webapps/solr/WEB-INF/lib/
   ```

   复制solr-8.2.0/server/lib下以metrics开头的jar到solr目录：

   ```shell
   cp solr-8.2.0/server/lib/metrics* solr/tomcat8/webapps/solr/WEB-INF/lib/
   ```

   > 上面这两项注意是复制到solr服务的lib目录下，不是复制到tomcat8/lib下。

   复制solr-8.2.0/server/resources下的log4j*.xml文件到solr

   首先在solr创建classes目录：

   ```shell
   mkdir solr/tomcat8/webapps/solr/WEB-INF/classes
   ```

   复制日志配置文件：

   ```shell
   cp solr-8.2.0/server/resources/log4j2*.xml solr/tomcat8/webapps/solr/WEB-INF/classes/
   ```

   将solr-8.2.0/server/solr目录复制到solr/目录下，并重命名为solrhome:

   ```shell
   cp -r solr-8.2.0/server/solr solr/solrhome
   ```

   修改日志路径

   ```shell
   vim solr/tomcat8/webapps/solr/WEB-INF/classes/log4j2.xml
   ```

   指定fileName和filePattern的路径：

   ```xml
   <RollingRandomAccessFile
           name="MainLogFile"
           fileName="/opt/solr/solrhome/log/solr.log"
           filePattern="/opt/solr/solrhome/log/solr.log.%i" >
         <PatternLayout>
        ....
   ```

   关联solr及solrhome

   修改solr里的web.xml文件

   ```bash
   vim solr/tomcat8/webapps/solr/WEB-INF/web.xml
   ```

   web.xml中`<web-app></web-app>标签内`添加如下配置，指定sorlhome路径

   ```xml
   <env-entry>
       <env-entry-name>solr/home</env-entry-name>
       <env-entry-value>/opt/solr/solrhome</env-entry-value>
       <env-entry-type>java.lang.String</env-entry-type>
   </env-entry>
   ```

   注释掉下方的下列配置：

   ```xml
   <!--
   <security-constraint>
       <web-resource-collection>
           <web-resource-name>Disable TRACE</web-resource-name>
           <url-pattern>/</url-pattern>
           <http-method>TRACE</http-method>
   	</web-resource-collection>
   	<auth-constraint/>
   </security-constraint>
   <security-constraint>
   	<web-resource-collection>
           <web-resource-name>Enable everything but TRACE</web-resource-name>
           <url-pattern>/</url-pattern>
           <http-method-omission>TRACE</http-method-omission>
   	</web-resource-collection>
   </security-constraint>
   -->
   ```

   最后启动tomcat，访问服务器的solr服务：

   ```bash
   sh solr/tomcat8/bin/start.sh
   ```

   访问地址:

   ```
   localhost:8080/solr/index.html
   ```

   ## 三、配置IK分词器

   首先从[IK分词器](https://search.maven.org/search?q=com.github.magese)下载与solr版本匹配的jar包，并放置在solr服务的lib目录下，

   ```bash
   cp ik-analyzer-8.2.0.jar solr/tomcat8/webapps/solr/WEB-INF/lib/
   ```

   在solr/solrhome/下创建目录test_core，拷贝配置文件到test_core中：

   ```bash
   cp -r solr/solrhome/configsets/sample_techproducts_configs/conf/ solr/solrhome/test_core/
   ```

   修改conf中的solr.xml文件，修改jar路径:

   ```xml
   <lib dir="${solr.install.dir:../}/contrib/extraction/lib" regex=".*\.jar" />
   <lib dir="${solr.install.dir:../}/dist/" regex="solr-cell-\d.*\.jar" />
   
   <lib dir="${solr.install.dir:../}/contrib/clustering/lib/" regex=".*\.jar" />
   <lib dir="${solr.install.dir:../}/dist/" regex="solr-clustering-\d.*\.jar" />
   
   <lib dir="${solr.install.dir:../}/contrib/langid/lib/" regex=".*\.jar" />
   <lib dir="${solr.install.dir:../}/dist/" regex="solr-langid-\d.*\.jar" />
   
   <lib dir="${solr.install.dir:../}/dist/" regex="solr-ltr-\d.*\.jar" />
   
   <lib dir="${solr.install.dir:../}/contrib/velocity/lib" regex=".*\.jar" />
   <lib dir="${solr.install.dir:../}/dist/" regex="solr-velocity-\d.*\.jar" />
   ```

   修改managed-schema文件，添加ik分词器配置：

   ```xml
   <!-- ik分词器 --> 
   <fieldType name="text_ik" class="solr.TextField"> 
   	<analyzer type="index"> 
   		<tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="false" conf="ik.conf"/> 
   		<filter class="solr.LowerCaseFilterFactory"/> 
   	</analyzer> 
   	<analyzer type="query"> 
   		<tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="true" conf="ik.conf"/> 
   		<filter class="solr.LowerCaseFilterFactory"/> 
   	</analyzer> 
   </fieldType>
   ```

   ## **了解solrhome**

   1、collection1：是一个solrcore，一个solrcore就是一个索引库。一个solr服务器上可以有多solrcore。每个索引库之间是相互独立的。

   2、\solrhome\collection1\conf：是存放每个solrcore的个性配置。

   3、Solrconfig.xml

   a) luceneMatchVersion：匹配lucene的版本信息

   b) Lib：solrcore扩展使用的jar包。默认值是collection1\lib，如果没有此文件夹就创建一个。

   c) dataDir：索引库存放的目录。默认是collection1\data文件夹。如果没有solr会自动创建。如果想修改为其他位置，需要配置此节点。

   d) requestHandler：配置solr对外提供服务的url

   i. <requestHandler name="/select" class="solr.SearchHandler">：查询索引库使用的url

   ii. <requestHandler name="/update" class="solr.UpdateRequestHandler">

   维护索引库使用的url

   e) defaultQuery：管理页面默认的查询条件 *:*

   4、Core.properties：配置了solrcore的名字。

   第六步：告诉solr工程solrhome的位置。修改solr/WEB-INF/web.xml文件。

   第七步：启动tomcat

   访问http://localhost:8080/solr

   

   ## 索引的维护

   在solr中域必须先定义后使用。而且每个document中必须有一个id域。

   **5.5版本以下不包括5.5 Schema.xml**  

   5.5版本以上包括5.5  managed-schema

   ##### Field:域的定义。

- Name：域的名称
- Type：域的类型
- Indexed：是否索引
- Stored：是否存储
- multiValued：是否多值，如果是多值在一个域中可以保持多个值。

##### dynamicField动态域

- Name：域的名称，是一个表达式。如果域的名称和表达式相匹配，此域名就可以使用。
- Type：域的类型
- Indexed：是否索引
- Stored：是否存储
- multiValued：是否多值，如果是多值在一个域中可以保持多个值。

##### uniqueKey

- 每个文档必须有一个uniqueKey，而且不能重复。相当于表中的主键。

##### copyField

- 复制域。
- Source：源域
- Dest：目标域。
- 创建文档时，solr会自动把源域的内容复制到目标域。使用复制域可以提供查询的性能。

##### fieldType

- 域的类型。
- Name：域类型名。
- Class：对应的实现类。solr.TextField类似于Lucene中的TextField。可以配置用户自定义的分析器。

##### 自定义fieldType使用中文分析器

###### 配置中文分析器

配置步骤：

- 第一步：把IKAnalyzer2012FF_u1.jar添加到solr工程的lib库中。
- 第二步：把配置文件和扩展词典、停用词词典添加到solr工程classpath下。Solr/WEB-INF/classes。保证字典的字符集是utf-8.

###### 配置自定义fieldtype

在schema.xml中添加如下内容：

```xml
<!-- IKAnalyzer-->

<fieldType name="text_ik" class="solr.TextField">

<analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>

</fieldType>

<!--IKAnalyzer Field-->
```

**配置自定义的域**

```xml
<field name="title_ik" type="text_ik" indexed="true" stored="true" />

<field name="content_ik" type="text_ik" indexed="true" stored="false" multiValued="true"/>
```

#### Dataimport插件

##### 安装步骤：

第一步：把dataimport插件依赖的jar包添加到collection1\lib文件夹下。

第二步：把mysql的数据库驱动也放到collection1\lib文件夹下

第三步：修改solrhome/collection1/conf/solrconfig.xml，添加一个requestHandler。

```xml
<requestHandler name="/dataimport"

class="org.apache.solr.handler.dataimport.DataImportHandler">

<lst name="defaults">

<str name="config">data-config.xml</str>

</lst>

</requestHandler>
```

第四步：创建一个data-config.xml。目录和solrconfig.xml在同一个目录下collection1\conf。

```xml
<?xml version="1.0" encoding="UTF-8" ?><dataConfig><dataSource type="JdbcDataSource"driver="com.mysql.jdbc.Driver"url="jdbc:mysql://192.168.33.10:3306/taotao"user="root"password="root"/><document><entity name="product" query="SELECT pid,name,catalog_name,price,description,picture FROM products "><field column="pid" name="id"/><field column="name" name="product_name"/><field column="catalog_name" name="product_catalog_name"/><field column="price" name="product_price"/><field column="description" name="product_description"/><field column="picture" name="product_picture"/></entity></document></dataConfig>
```

第五步：重启tomcat后，进入solr管理页面，执行数据导入



#### 索引库的查询

##### 查询语法支持的参数

q：主查询条件。完全支持lucene语法。还进行了扩展。

fq：过滤查询。是在主查询条件查询结果的基础上进行过滤。

sort：排序条件。排序的域asc。如果有多个排序条件使用半角逗号分隔。

start, rows：分页处理。Start起始记录rows每页显示的记录条数。

fl：返回结果中域的列表。使用半角逗号分隔。

df：默认搜索域

wt：响应结果的数据格式，可以是json、xml等。

hl：开启高亮显示。

hl.fl：要高亮显示的域。

hl.simple.pre：高亮显示的前缀

hl.simple.post：高亮显示的后缀

### SolrJ客户端

可以实现对索引库的增删改查操作。

###### 使用步骤：

第一步：创建一java工程。

第二步：导入jar包。导入solrj的jar 包。

#### 索引库的维护

##### 添加文档

第1步：创建SolrServer对象和服务端建立连接。HttpSolrServer子类来完成。集群环境使用CloudSolrServer。

第2步：创建一文档对象。SolrInputDocument。

第3步：向文档对象中添加域。使用addField添加域。要求必须有id域，而且每个域必须在schema.xml中定义。

第4步：使用solrServer对象把文档提交到服务器。