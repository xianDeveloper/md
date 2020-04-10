# [jsqlparser和calcite和druid功能对比](https://www.cnblogs.com/zhihuifan10/p/11124724.html)

**需求分析：（用其它方法替代metabase中的某些功能）**
功能1.通过对sql查询语句的分析，得到所有表名，以及所有表的字段名，字段类型，字段注解信息。
功能2.在sql语句执行查询前，校验sql语句是否正确，得到校验后的错误信息。

带着这个需求，我去学习并测试了jsqlparser和calcite和以及druid的相关功能，并在这里记录自己测试的结果
（该结果只针对sql查询语句的解析）

**jsqlparser：**
上手容易，操作简单，只能对sql语句进行拆分解析，和数据库无关。
**calcite:**
功能强大，操作相对jsqlparser复杂一点，对sql语句的解析功能相对jsqlparser来说很强大，
可以和数据库建立查询，在jsqlparser解析结果的基础上还可以得到字段的类型和字段注解。
**druid：**
sql解析功能和jsqlparser类似，只能对sql语句进行拆分解析；如果用sql监控功能建立sql语句的结果分析，得到的结果和未建立数据库连接前一样。

**相关api：**
jsqlparser：
http://jsqlparser.sourceforge.net/docs/
calcite：
http://calcite.apache.org/apidocs/index.html
druid:
http://tool.oschina.net/apidocs/apidoc?api=druid0.26

**相关学习文档和测试代码：**
jsqlparser
链接：https://www.cnblogs.com/zhihuifan10/articles/11124953.html
calcite 
druid 
(后面会补上，待续...)

[有没有好用的开源sql语法分析器？](zhihu.com/question/51676071)  presto 和 calcite presto，hive，drill，calcite，sparksql

之前工作需要参考了不少开源jvm的sql前端的代码，包括presto，hive，drill，calcite，sparksql和一些现在记不得名字的。可以相当负责任地说，presto的代码是开源代码里质量最高的一批了。之后我们把presto前端一直到语义分析层切开拿做他用，整个切割过程异常顺利，也是得益于p的代码干净整洁和模块设计清晰。hive的不提了，多年的项目臃肿庞大而且并不漂亮；sparksql在你看完presto之后再看会有似曾相识的赶脚；calcite的代码并不是很容易理解，至少对我来说相对晦涩，drill在看的时候并没有成型就放弃了。

再回到你的问题，你似乎要做的是血缘分析，现成的代码其实也有，就是hive里有个工具类叫lineageinfo，你可以看看。如果是对传统数据库使用，你可能需要把hive的语义层也切出来，因为你要用的信息属于元信息解析之后的结果了，并不是语法分析就可以搞定的，比如子查询回溯外层源表，并不能简单靠语法分析搞定，当然自己在语法分析上改也不困难。



**结论总结:**
只有calcite可以得到需求中需要的结果，但是有些函数在calcite中不支持，例如mysql中的group_concat 函数，在执行sql解析时抛出函数不存在异常；
为了解决这个功能，我测试了calcite添加内置函数，但是这个功能有局限性，不太适用我的需求场景，现在未找到方法来替代metabase中解析sql功能。