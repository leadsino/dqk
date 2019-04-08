# NoSQL扫描器(mongoaudit)

主机：10.10.100.88     root:pldsec

## 1. mongoaudit（一款测试工具）

 	github地址：https://github.com/stampery/mongoaudit

​	mongoaudit是一个CLI工具，用于审计MongoDB服务器，检测较差的安全设置并执行自动渗透测试。

​	mongoaudit可以检测mongodb中的不安全的配置，已知的mongodb漏洞以及其他问题，并可以建议使用者如何去修复这些问题。

### 1.1 支持的测试包括：

#### 	1.1.1 安全配置

​	（1）检测mongodb是否监听与默认端口不同的端口

​	（2）检测mongod服务器是否只允许白名单内的主机、网段连接

​	（3）检测能否在28017上访问mongodb http状态接口

​	（4）检测mongodb是否公开了其版本号

​	（5）检测mongodb版本是否高于2.4版本

​	（6）检测是否启用TLS/SSL加密

​	（7）检测是否开启身份验证

​	（8）检测是否启用SCRAM-SHA-1身份验证方法

​	（9）检测服务器端JS是否被禁用（需要有效身份验证凭据）

​	（10）检测授予用户的角色仅允许CRUD操作（需要有效身份验证凭据）

​	（11）检测用户是否只拥有单个数据库的权限（需要有效身份验证凭据）

#### 	1.1.2 覆盖的安全漏洞（比较全的覆盖了2015年之前官网披露的一些mongodb漏洞）

​	（1）安全漏洞CVE-2015-7882

​	问题摘要：MongoDB Enterprise 3.0.0到3.0.6中的漏洞可能允许用户未经授权访问MongoDB实例或群集。

​	用户影响：在启用LDAP身份验证的情况下，可以对运行受影响的MongoDB Enterprise版本的实例或群集进行未经授权的访问。**MongoDB的社区版不受此漏洞的影响。**

​	解决方法：此问题没有解决方法。受影响的用户必须尽快升级到MongoDB 3.0.7。

​	（2）安全漏洞CVE-2015-2705

​	问题摘要：由于未能检查缺失值，MongoDB容易受到拒绝服务（崩溃）的影响。使用身份验证运行时，攻击者需要成功通过身份验证到MongoDB并具有对数据库的写入权限才能利用此漏洞。

​	用户影响：远程攻击者可能导致拒绝服务（崩溃）。

​	解决方法：改进受影响区域的验证。

​	（3）安全漏洞CVE-2014-8964

​	问题摘要：[CVE-2014-8964](https://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2014-8964)是针对第三方PCRE库中的漏洞而发布的。

​	用户影响：（2.6.8 and earlier, 3.0.0）MongoDB版本通过特制的正则表达式远程触发拒绝服务（崩溃）。

​	解决方法：MongoDB(2.6.9和3.0.1)已修复。

​	（4）安全漏洞CVE-2015-1609

​	问题摘要：（2.6.7及更早版本，2.4.12及更早版本）mongod服务器无法验证某些格式错误的BSON。此故障发生在预身份验证之前。

​	用户影响：特制的，格式错误的BSON消息可能会在服务器中触发未捕获的异常，从而导致可用性丢失。

​	解决方法：MongoDB（2.6.8和2.4.13）已修复。

​	（5）安全漏洞CVE-2014-3971

​	问题摘要：

​	用户影响：启用X.509身份验证时远程触发崩溃。

​	解决方法：MongoDB（2.6.2）已修复。

​	（6）安全漏洞CVE-2014-2917

​	问题摘要：

​	用户影响：用户凭证的信息泄露

​	解决方法：MongoDB（2.6.1）已修复。

​	（7）安全漏洞CVE-2013-4650

​	问题摘要：

​	用户影响：在**本地**以外的数据库上不正确地授予用户系统权限。

​	解决方法：MongoDB（2.4.5,2.5.1）已修复。

​	（8）安全漏洞CVE-2013-3969

​	问题摘要：

​	用户影响：Javascript引擎中的远程触发分段错误。

​	解决方法：MongoDB（2.4.5,2.5.1）已修复。

​	（9）安全漏洞CVE-2012-6619

​	问题摘要：2.3.2之前的MongoDB的默认配置不验证对象，这允许远程通过身份验证的用户通过插入命令中的列名中的精心设计的BSON对象导致拒绝服务（崩溃）或读取系统内存，从而触发缓冲区过阅读。

​	（10）安全漏洞CVE-2013-1892

​	问题摘要：2.0.9之前的MongoDB和2.2.4之前的2.2.x没有正确验证SpiderMonkey中对nativeHelper函数的请求，这允许远程认证用户导致拒绝服务（无效的内存访问和服务器崩溃）或通过执行任意代码执行在第一个参数中制作了内存地址。

​	（11）安全漏洞CVE-2015-2132

​	问题摘要：HP HP-UX B.11.11，B.11.23和B.11.31中的execve系统调用实现中的未指定漏洞允许本地用户通过未知向量获取权限。

#### 	1.1.3 **未覆盖的一些官网披露的安全漏洞：**（mongodb的安全性漏洞大部分在官网披露并修复，暂时未找到现成可直接利用的漏洞代码）

​	（1）安全漏洞CVE-2017-15535

​	问题描述：启用有线协议压缩后，恶意攻击者可能利用现有漏洞拒绝服务或修改服务器内存。

​	影响版本：3.4.0至3.4.9

​	在版本中修复：3.4.10

​	（2）安全漏洞CVE-2017-12926

​	问题描述：使用WiredTiger存储引擎时，可以发出管理命令将统计日志写入特定文件。

​	影响版本：3.0.0-3.0.14（启用WiredTiger），3.2.0-3.2.8

​	在版本中修复：3.0.14,3.2.8

​	（3）安全漏洞CVE-2016-6494

​	问题描述：MongoDB中的客户端使用.dbshell历史文件的全局可读权限，这可能允许本地用户通过读取这些文件来获取敏感信息。

​	影响版本：2.0,2.2,2.4,2.6,2.8,3.014及更早版本，3.2.14及更早版本

​	在版本中修复：3.0.15,3.2.14,3.4

​	（4）安全漏洞CVE-2016-3104

​	问题描述：允许远程攻击者在针对不存在的数据库进行身份验证时利用内存数据库表示来导致拒绝服务（内存消耗和进程终止）。

​	影响版本：2.4,2.6

​	在版本中修复：2.6

### 1.2 下载方式

​	wget "https://pypi.python.org/packages/source/p/pip/pip-1.5.4.tar.gz#md5=834b2904f92d46aaa333267fb1c922bb"  --no-check-certificate

​	pip install mongoaudit

### 1.3 使用

​	输入目标IP——自动检测——产生检测结果（符合passed/不符合failed）



## 2. NoSQLAttack（简单的扫描器）

​	NoSQLAttack是一个开源Python的工具，可以自动在互联网上公开的MongoDB服务器的IP，并通过MongoDB的默认配置弱点和注入攻击来泄露数据库数据。目前，该项目主要关注的MongoDB。

### 2.1 扫描过程

![1554704003823](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554704003823.png?raw=true)

#### 	2.1.1 公网扫描

​	（1）通过shodan扫描公网IP（暴露在公网上的存在Mongod服务的主机）

![1554704409732](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554704409732.png?raw=true)

​	（2）扫描指定的主机

![1554704563417](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554704563417.png?raw=true)

​	获取mongodb版本及平台

![1554704767557](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554704767557.png?raw=true)

​	存在的数据库、表及用户（做了安全配置的数据库无法查看）

![1554704881341](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554704881341.png?raw=true)

​	复制指定的数据库（需要对应的用户名密码）

![1554704984759](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554704984759.png?raw=true)

#### 	2.1.2 指定IP扫描

![1554705387183](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554705387183.png?raw=true)

​	获取版本及平台，存在的数据库、表及用户，复制数据库过程与2.1.2相同。

![1554705426108](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554705426108.png?raw=true)

#### 	2.1.3  注入攻击

​	指定主机+端口+url进行注入攻击（应该是对使用mongodb的应用程序进行攻击（默认的是简单的sql注入，也可以自己定义））



## 3 Nosql-Exploitation-Framework（专业的扫描器）

​	NoSQL扫描和利用框架的FrameWork。

+ 为Mongo，Couch，Redis，H-Base，Cassandra增加支持的首款工具
+ 支持NoSQL WebAPPS
+ 添加了JS Injection，Web应用程序Enumeration的有效负载列表。
+ 扫描支持Mongo，CouchDB和Redis
+ Mongo，Couch和Redis的字典攻击支持
+ 为数据库添加的枚举模块，以db的@ one shot检索数据。
+ 目前发现Mongo的Web界面
+ Shodan查询功能
+ 多线程IP列表扫描程序
+ 为CouchDB添加了转储和复制数据库功能
+ 嗅闻Mongo，Couch和Redis

### 3.1 针对不同数据库实现的功能集

​	

|                  Mongo                   |          CouchDB           |               Redis               |
| :--------------------------------------: | :------------------------: | :-------------------------------: |
| 检查并枚举Mongo系统/配置信息和REST API。 |       检查CouchAPI。       |          枚举Redis配置。          |
|       支持枚举数据库，集合和数据。       |       字典攻击模块。       |       枚举Redis键，字符串。       |
|            检查连接的客户端。            | 支持枚举数据库，表和数据。 |   用于检查Redis文件枚举的模块。   |
|              字典攻击模块。              |                            | 用于检查Redis LUA RCE漏洞的模块。 |
|           目前检查已知的CVE。            |                            |          字典攻击模块。           |

### 3.2 用法

​	github地址：https://github.com/torque59/Nosql-Exploitation-Framework/wiki/Usage

![1554715334893](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554715334893.png?raw=true)

![1554715363536](https://github.com/leadsino/dqk/blob/master/typora-user-images/1554715363536.png?raw=true)

# 附录

[1].mongodb alerts（官方披露的问题（安全））https://www.mongodb.com/alerts

[2].已知CVE号搜索相应的漏洞信息https://jira.mongodb.org/browse/WT-4705?jql=

[3].mongodb安全性配置https://docs.mongodb.com/manual/security/#security