title: "Resume"
date: 2015-04-09 20:12:45

---

# 联系方式

- 手机：18302118258
- Email：wangx89@126.com
- 微信号：wangxu_89

---
# 个人信息
 - 王旭/男/1989
 - 本科/东南大学成贤学院  （计算机工程系2009/09～2013/06）
 - 硕士研究生/上海大学  （计算机工程与科学学院2013/09～2016/03）
 - 工作年限：应届生
 - 技术博客：[http://blog.csdn.net/shijiebei2009](http://blog.csdn.net/shijiebei2009)
 - Github：[https://github.com/shijiebei2009](https://github.com/shijiebei2009)
 - 个人站点代码馆：[http://codepub.cn](http://codepub.cn)
 - 户籍：江苏省徐州市睢宁县
 - 通讯地址：上海市宝山区上大路99号上海大学校内三号楼
 - 期望职位：①Hadoop/大数据初级程序员 ②Java程序员 ③Python工程师
 - 期望薪资：面谈
 - 期望城市：上海、杭州、南京

---
# 工作/项目经历

## 大众点评网(2015/6 ~ Now)
### POI（Point of Interest）与基础内容
####菜单匹配服务
将用户点评菜单与系统中商铺输入菜单进行匹配，前期使用Python，后期使用Java进行了重构。使用到的匹配方案是：
* 精确匹配
* 基于词典精确匹配
 - 构建量词词典，对菜单进行格式化
 - 构建后缀词典，对菜单进行格式化
* 近似模糊匹配
 - 比较编辑距离
 - 计算公共子序列(有序，非连续)
 - 计算公共子串(有序且连续)
 - 根据评分公式计算相似度并辅之以动态变化的阈值

####商户判重服务的客户端
封装了商户判重的Pigeon服务并对外提供两个接口，接收包含商户详细信息的CSV格式输入文件，该文件有两种格式，其一两两商户以成对形式出现，通过GroupId进行标识，其中之一为其他商户信息称为基准POI，另一个商户为点评商户，调用两两匹配接口，判断该对商户之间的关系。另一接口为接收全部是基准POI信息的商户，从点评已有商户中查询与之匹配的商户。

该客户端供运营人员使用，主要目的是对判重服务进行人工抽检，计算判重服务的准确率、召回率以及新增重复率。

####商户判重服务
由爬虫组负责从其他网站上抓取商户信息，与点评已有的两千七百万家商户信息进行比较。如果是与点评已有商户不重复的需要新增上线，与点评已有商户不确定关系的需要人工介入，与点评已有商户重复或可能重复的需要将关系入库。

使用内部研发框架Pigeon2，该框架主要解决如下问题
* 分布式服务通信框架（RPC），当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，通过Pigeon可以实现应用之间的交互
* 基于Pigeon实现服务监控治理、资源调度等

使用内部研发框架Lion，该框架是一个配置管理平台，可以实时推动配置变更，管理Service的服务地址。

在商户比较中，利用到的信息字段主要如下

 - 商户名（别名、商户名、结构化商户名、分店名）
 - 地址（地址、结构化地址、交叉路、经纬度）
 - 富信息（电话、类别【频道（例亲子、结婚、美食、酒店）、类目信息】）

在商户比较中，使用到的主要特征策略如下

 - 商户名
     * 模糊比较（编辑距离、杰卡德相似系数、包含关系【头包含、尾包含、连续包含、非连续包含】）
     * 结构化比较（主干比较、分支比较、总体结构比较）
 - 地址
     - 结构化比较（商区、区县、乡镇、街道、楼层、弄、门牌）
     - 模糊比较
     - 距离比较（基于经纬度）

####其他项目
>判重服务的内部分档与业务分档关系配置平台，属于Web平台开发，主要是CRUD操作。
使用Selenium模拟用户登录支付宝账户，并自动根据交易号或者商户订单号查询交易对方信息。

## 张江徐汇科企联研资源共享平台(2014/8 ~ 2014/11)

### 文档分析模块
在该项目中主要负责：导入word/pdf格式文档、文档基本统计信息提取、文档格式化信息抽取、文档摘要抽取、站内文档搜索引擎接口（基于Lucene）、根据关键词词频的文档匹配。在下面讲义部分会提供PPT演示地址。

## 北京科蓝软件系统有限公司(上海分公司)(2013/7 ~ 2013/9)

### 江苏农信项目 
使用科蓝公司内部框架及系统进行江苏农信网上银行的开发工作，由于工作时间较短，仅完成了一个发版申请；发版项目名称：支付签约管理；发版事由：支付签约管理统计开销户数。使用公司内部研发框架，在入职前期对我们做了一些银行业务方面的培训，并花了一定时间用来熟悉框架、使用、以及配置上等。

---
# 开源项目和作品
## 开源项目
 - [Automatic Annotation](https://github.com/shijiebei2009/CEC-Automatic-Annotation) : 该项目主要是基于CEC语料库的分析，使用序列模式挖掘算法挖掘事件要素识别规则，包括词性规则、命名实体规则、句法分析规则等，将互联网上获取的突发事件类生语料经过哈工大LTP分析，获取分词、命名实体识别、句法分析、语义角色标注、依存分析等一系列结果，基于此结果做进一步的处理，然后完成对突发事件类生语料的自动标注工作
 - [在线观看](http://v.youku.com/v_show/id_XOTEzNDcyOTQ0.html)
 - [视频下载](http://pan.baidu.com/s/1nt62S7R) :针对自动标注（Automatic annotation） 项目进行视频介绍与说明

## 技术文章

- [使用Nexus创建私服](http://blog.csdn.net/shijiebei2009/article/details/41924965)
- [使用Maven下载Spring](http://blog.csdn.net/shijiebei2009/article/details/41872081)
- [Hadoop2.x介绍与源码编译](http://blog.csdn.net/shijiebei2009/article/details/40716517)
- [百度云平台使用说明](http://blog.csdn.net/shijiebei2009/article/details/44307923)
- [封装LTP4J的本地LTML调用接口](http://codepub.cn/2015/05/13/Local-call-interface-of-LTP4J-project-for-LTML-encapsulation/)
- [编译哈工大语言技术平台云LTP（C++）源码及LTP4J（Java）源码][id]
[id]: http://codepub.cn/2015/05/07/Compile-the-Language-Technology-Platform(C++)-and-LTP4J(Java)source-code/

## 演讲和讲义
  - 2014高智公司项目进展研讨会：[关键技术介绍](http://pan.baidu.com/s/1eQgsPeM)
  - 2014高智公司项目进展研讨会：[版本更新](http://pan.baidu.com/s/1c02H4D6)

# 技能清单

- Web开发：Java/PHP
- Web框架：Struts2/Hibernate/Spring/MyBatis
- 前端框架：Bootstrap/HTML5/JQuery
- 数据库相关：MySQL/MongoDB/Oracle/SQLServer
- 版本管理、文档和自动化部署工具：CVS/Svn/Git
- 项目构建：Ant/Maven(Nexus)
- 单元测试：JUnit
- 开源框架：Lucene、Heritrix
- 云和开放平台：新浪SAE
- 业余玩耍：Scala/Python/Android/CeontOS/Hadoop
- NLP相关：IKanalyzer/Paoding/FudanNLP/LTP/Stanford coreNLP/RDF/OWL/Jena
- 开发工具：Eclipse/IntelliJ IDEA

---
# 致谢
感谢您花时间阅读我的简历，期待能有机会和您共事。


  [1]: http://7xig3q.com1.z0.glb.clouddn.com/dianping-logo.jpg