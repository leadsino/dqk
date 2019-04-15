### 								MongoDB使用

#### 1.简介

​	MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。
​	在高负载的情况下，添加更多的节点，可以保证服务器性能。
​	MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。
​	MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。

​	特点：
​	（1）面向文档
​	（2）设置索引
​	（3）支持丰富的查询表达式
​	（4）扩展性强，分片
​	（5）支持多种语言

​	概念：

|   SQL概念   | MongoDB概念 |    解释     |
| :---------: | :---------: | :---------: |
|  database   |  database   |   数据库·   |
|    table    | collection  |   表/集合   |
|     row     |  document   |   行/文档   |
|   column    |    field    | 数据字段/域 |
|    index    |    index    |    索引     |
| table joins |             |   表连接    |
| primary key | primary key |    主键     |

#### 2. 启动与连接

#### 3.操作

|       命令        |      描述       |
| :---------------: | :-------------: |
| use database_name | 创建/切换数据库 |
|     show dbs      | 查看所有数据库  |
|        db         | 查看当前数据库  |
| db.dropDatabase() |   删除数据库    |

创建集合：

​	db.createCollection(name,options) 

​	name:集合名

​	options:可选参数

|    参数     |                             描述                             |
| :---------: | :----------------------------------------------------------: |
|    size     |           (可选)数值，为固定集合指定最大值（字节）           |
|     max     |         (可选)数值，指定固定集合中包含文档的最大数量         |
|   capped    | (可选) 布尔类型，如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。当该值为 true 时，必须指定 size 参数。 |
| autoIndexId | (可选）布尔类型，如为 true，自动在 _id 字段创建索引。默认为 false。 |

删除集合 ：

​	db.collection（集合名）.drop()  

查看已有集合：

​	show collections

插入文档（BSON）：

​	BSON与JSON相比：

​		遍历速度更快（每个元素的长度存在元素头部）

​		操作更简易（指定数据存储类型：9，10）

​		增加了额外的数据类型（增加了byte array，无需转换）

​	        db.Collection_name.insert(document)

更新文档：

​	update:	db.collection.update(

 				{

​				query,                  

​				update,

​				upsert:<boolean>,

​				multi:<boolean>	,

​				writeConcern:<document>

​				}

​			)

​	save: 	db.collection.save(

​				<document>,

​				{

​				writeConcern:<document>

​				}

​			)

删除文档：

​	db.collection.remove(

​		<query>,

​		{

​			justOne:<boolean>

​			writeConcert:<document>

​		}

​	)

查询文档：

​	db.collection.find(query,projection).pretty()

条件查询：

|  =   | -$eq  |
| :--: | :---: |
|  \>  | -$gt  |
|  <   | -$lt  |
| \>=  | -$gte |
|  <=  | -$lte |

索引：提高查询效率

​	db.collection.createIndex(key,options)

聚合：处理数据

​	db.collection.aggregate(AGGREGATE_OPERATION)

​	表达式举例：$sum,$avg，

​	管道：

​	表达式举例：$project，$group，$match，$limit，$geoNear(接近某一地理位置的有序文档)

#### 4.1复制

目的：

​	保障数据的安全性

​	数据高可用性 (24*7)

​	灾难恢复

​	无需停机维护（如备份，重建索引，压缩）

​	分布式读取数据

复制原理：

​	mongodb的复制至少需要两个节点。其中一个是主节点，负责处理客户端请求，其余的都是从节点，负责复制主节点上的数据。

​	mongodb各个节点常见的搭配方式为：一主一从、一主多从。

​	主节点记录在其上的所有操作oplog，从节点定期轮询主节点获取这些操作，然后对自己的数据副本执行这些操作，从而保证从节点的数据与主节点一致。	

![1552547915244](C:\Users\dqk\AppData\Roaming\Typora\typora-user-images\1552547915244.png)

​		创建副本集，添加成员（只能从主节点的添加）（判断是否为主节点db.isMaster()）	

#### 4.2.分片

​	在Mongodb里面存在另一种集群，就是分片技术,当MongoDB存储海量的数据时，一台机器可能不足以存储数据，也可能不足以提供可接受的读写吞吐量。这时，我们就可以通过在多台机器上分割数据，使得数据库系统能存储和处理更多的数据。

![1552550443498](C:\Users\dqk\AppData\Roaming\Typora\typora-user-images\1552550443498.png)

​	**Shard**:用于存储实际的数据块。

​	**Config Server**：存储整个ClusterMetadata(集群元数据——关于数据的[组织]、数据域及其关系的信息，数据的数据)

​	**Query Routers**:前端路由，是整个集群看起来像单一数据库。

#### 4.3.备份与恢复

​	mongodump -h dbhost -d dbname -o  dbdirectory

​	mongorestore -h \<hostname><:port> -d dbname  \<path>

#### 4.4监控

​	工具mongostat(自带)，状态检测工具；mongotop（自带）跟踪一个MongoDB实例。

#### 5.关系

​	文档间的嵌入和引用（通过引用文档的 **id** 字段来建立关系。）。

#### 6.数据库引用

​	一个文档（行）从多个集合（表）引用文档——使用DBRefs。

​	{$ref(集合):,$id（引用的id，行）:,$db:（数据库名，可选）}

#### 7.覆盖索引查询

​	创建一个索引之后，索引覆盖的查询语句都会从索引中直接中提取数据

#### 8.原子操作

​	mongodb不支持事务，无法保证数据的完整性。

​	但提供了许多原子操作，如文档的保存，修改，删除等。

#### 9.GirdFS

​	存储和回复那些超过16M(BSON文件限制)的文件。

​	fs.files()、fs.chunks()

#### 10. Map Reduce

​	将大批量的工作分解（MAP）执行，再将结果合并成最终结果（Reduce）。常用于大数据分析。

​	

​	