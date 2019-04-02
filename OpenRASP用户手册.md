# OpenRASP用户手册

## 1. 简介

​	Gartner在2014年提出了运行时应用自我保护（RASP）技术的概念，即对应用服务的保护，不应该依赖于外部系统；应用应该具有自我保护的能力。它不同于传统WAF——拦截http数据包，然后使用规则对数据包进行匹配扫描，若没有匹配则放行的工作原理； RASP审查的是最终要执行的代码，它通过拦截将要执行的代码，对代码进行规则匹配，若未匹配则放行代码。

​	OpenRASP（RunTime Application self-protection）是该技术的开源实现，它改变了防火墙依赖请求特征来拦截攻击的模式。对于注入类的漏洞，它可以识别用户输入的部分，并检查程序逻辑是否被修改。由于不依赖请求特征，它每条报警都是成功的攻击。 

## 2. 安装部署

​	目前，OpenRASP 支持 Java 和 PHP 两种开发语言。

​	兼容性说明：

| Java Agent                    |                                                              |
| :---------------------------- | :----------------------------------------------------------- |
| 操作系统                      | RHEL 6.X ~ 7.X<br />CentOS 6.X ~ 7.X<br />Ubuntu 14+<br />MacOS 10.10+ |
|                               |                                                              |
|                               |                                                              |
| Web 服务器 - 支持 docker 部署 | Tomcat 6 ~ 8<br />Jetty 7 ~ 9<br />JBoss 4 ~ 6<br />Resin 3 ~ 4<br />SpringBoot 1 ~ 2<br />WebSphere |
|                               |                                                              |
| JDK版本                       | Oracle JDK 6~8<br />OpenJDK 6~8                              |
|                               |                                                              |
| 数据库JDBC驱动                | MySQL 5.1.X ~ 6.0.X<br />SQLServer 6.1.X ~ 6.2.X<br />SQLite 3.5.X ~ 3.19.X<br />Oracle 9.0.X ~ 12.2.X<br />PostgreSQL 8.0.X ~ 9.4.X<br />DB2 4.2.X ~ 4.23.X |



| php Agent    |                                                              |
| :----------- | ------------------------------------------------------------ |
| SAPI支持范围 | php命令行服务器<br />PHP-FPM<br />Apache PHP模块             |
|              |                                                              |
| 操作系统     |                                                              |
| Linux        | PHP5.3 ~ 5.6，7.0 ~ 7.2<br />     Ubuntu 14.04+<br />     CentOS 6 ~ CentOS 7<br />     其他 glibc >= 2.12 的发行版 |
| Mac OS       | Homebrew PHP 5.6，7.0 ~ 7.2 (没有深入测试)                   |
|              |                                                              |
| 数据库       | PDO<br />MySQL、MySQLi<br />PG<br />SQLite3                  |

### 2.1 管理后台的安装

### 2.2 软件安装

​	OpenRASP最新版本下载：https://github.com/baidu/openrasp/releases

​	OpenRASP源代码下载：https://github.com/baidu/openrasp

​	

```
（1）Tomcat服务器中OpenRASP的自动化安装
    #解压缩后，进入到解压后的目录，如果你要开启远程管理，请先参考 管理后台 - 添加主机 文档，找到 #app_id/app_secret/backend_url 三个关键参数，然后执行如下命令，
 	java -jar RaspInstall.jar -install <tomcat_root> -backendurl http://XXX -appsecret 2XXX -appid XXX
    #安装后，需要重启 Tomcat 服务器生效。<tomcat_root>是tomcat的根目录

    RASP升级方法：
    #在 Linux 下面，可以使用 
    bin/catalina.sh stop 
    #来执行 graceful stop，你会在日志里看到类似下面的输出，这表明 Tomcat 将在处理完当前的HTTP后退出
    #Jan 12, 2018 6:11:55 PM org.apache.catalina.core.StandardWrapper unload
    #INFO: Waiting for 1 instance(s) to be deallocated for Servlet [jsp]
    #Jan 12, 2018 6:11:56 PM org.apache.catalina.core.StandardWrapper unload
    #INFO: Waiting for 1 instance(s) to be deallocated for Servlet [jsp]
    #Jan 12, 2018 6:11:57 PM org.apache.catalina.loader.WebappClassLoaderBase #clearReferencesThreads
    #之后参考我们的自动或者手动安装文档，重新安装即可

(2)JBoss服务器中OpenRASP的自动化安装
    #解压缩后，进入到解压后的目录，如果你要开启远程管理，请先参考 管理后台 - 添加主机 文档，找到 #app_id/app_secret/backend_url 三个关键参数，然后执行如下命令，
    java -jar RaspInstall.jar -install <jboss_root> -backendurl http://XXX -appsecret XXX -appid XXX
    #安装成功后，需要重启 JBoss 服务器生效。

(3)Resin服务器中OpenRASP的自动化安装
    #解压缩后，进入到解压后的目录，如果你要开启远程管理，请先参考 管理后台 - 添加主机 文档，找到 #app_id/app_secret/backend_url 三个关键参数，然后执行如下命令，
    java -jar RaspInstall.jar -install <resin_root> -backendurl http://XXX -appsecret XXX -appid XXX
    #安装成功后，需要重启 Resin 服务器生效。

(4)Jetty服务器中OpenRASP的手动安装
  #手动安装
  #1. 安装软件
    #进入到 jetty 安装目录，e.g /opt/jetty复制安装包内的 rasp 目录到当前目录，
    cp -R ~/Downloads/rasp-20170721/rasp .
    #OpenRASP 需要在 rasp 目录下释放一些动态链接库，所以还需要修改 rasp 目录的权限，
    chmod 777 -R rasp
  #2. 配置 Jetty
    #假设 rasp 目录释放到了 /opt/jetty/rasp/，
    #请在 java 启动参数里添加如下内容，
    -javaagent:/opt/jetty/rasp/rasp.jar -Dlog4j.rasp.configuration=file:///opt/jetty/rasp/conf/rasp-log4j.xml
    #如果是命令行启动，请将 -jar 放在命令的最末尾
    java -javaagent:/opt/jetty/rasp/rasp.jar -Dlog4j.rasp.configuration=file:///opt/jetty/rasp/conf/rasp-log4j.xml -jar start.jar
    #需要注意的是，对于Windows服务器，请修正 -javaagent、-Dlog4j.rasp.configuration 参数，比如
    -javaagent:D:\jetty\rasp\rasp.jar -Dlog4j.rasp.configuration=file:///D:\jetty\rasp\conf\rasp-log4j.xml
  #3. 开启远程管理
    #配置方法同 Tomcat 开启远程管理，不再赘述。
  #4. 检查 Jetty 是否安装成功
    #重启 Jetty 服务器，然后检查日志文件，如果出现 OpenRASP Engine Initialized 字样，出现则说明安装成功；或者访问一下服务器，检查是否存在 X-Protected-By: OpenRASP 响应头，存在即表示安装成功。
 
（5）Spring Boot框架中OpenRASP的手动安装
	#war 包部署
	#如果将 Spring Boot 项目构建为 war 包，直接按照相应服务器版本的安装文档安装即可。
	#jar 包部署
	#如果将 Spring Boot 项目构建为内嵌服务器的 jar 包，则需要按照如下步骤安装。另外，embedded tomcat server 模式也是支持的。

	#1. 安装软件
	进入到 Spring Boot 安装目录，e.g /opt/spring-boot复制安装包内的 rasp 目录到当前目录，
cp -R ~/Downloads/rasp-20170721/rasp 
	#OpenRASP 需要在 rasp 目录下释放一些动态链接库，所以还需要修改 rasp 目录的权限，
chmod 777 -R rasp
	#2. 配置启动参数
	#假设 rasp 目录释放到了 /opt/spring-boot/rasp/，在启动 Spring Boot 的时候在启动参数里添加如下参数。其中，路径参数请使用绝对路径
-javaagent:"/opt/spring-boot/rasp/rasp.jar" -Dlog4j.rasp.configuration="file:///opt/spring-boot/rasp/conf/rasp-log4j.xml"
	#如果是命令行启动，请将 -jar 放在命令的最末尾，
java -javaagent:"/opt/spring-boot/rasp/rasp.jar" -Dlog4j.rasp.configuration="file:///opt/spring-boot/rasp/conf/rasp-log4j.xml" -jar springboot.jar
	#注意: rasp-log4j.xml 是启动时自动生成的，无需创建。
	#3. 开启远程管理
	#配置方法同 Tomcat 开启远程管理，不再赘述。
	#4. 检查安装是否成功
	#访问服务器，检查是否存在 X-Protected-By: OpenRASP 即可。
	
（7）php服务器中OpenRASP的自动化安装
	#解压缩后，进入到解压后的目录中，
	#如果你要开启远程管理，请先参考 管理后台 - 添加主机 文档，找到 app_id/app_secret/backend_url 三个关键参数，然后执行如下命令，
	php install.php -d /opt/rasp --backend-url http://myserver:port --app-secret XXX --app-id XXXX
	#其中 /opt/rasp 为 OpenRASP 安装目录，用于存储检测插件、报警日志等等，可根据实际情况调整。在某些情况下，php cli 和 php-fpm 使用的 ini 不同，自动安装程序可能会失败。
```

### 2.3 安装测试用例

​	为了方便你对软件进行测试，我们提供了一些测试用例。所有测试用例都在页面里附有说明，会告知你如何针对不同的平台，发起攻击，并测试拦截效果。具体有哪些漏洞环境，请参考 GitHub 页面:

​	下载测试用例最新版本：https://github.com/baidu-security/openrasp-testcases/releases

​	下载测试用例源代码：https://github.com/baidu-security/openrasp-testcases

​	用例使用说明：

​	在每个测试用例里，我们都增加了说明，即如何模拟正常的请求，和不正常的攻击请求。具体按照里面提供的 `CURL` 命令进行测试即可。安装测试用例后，你将会看到类似这样的页面:

![1553752568126](E:/GitHub/work/typora-user-images/1553752568126.png)

​	![1554184549028](E:/GitHub/work/typora-user-images/1554184549028.png)



​	攻击拦截说明：

​	当攻击被拦截，OpenRASP 会显示特定的拦截页面，以及当前 `request ID`。事后可根据这个ID去拦截日志里查找，定位原因。如果你发现 OpenRASP 无法拦截攻击，很有可能是安装没有成功，，并检查应用启动日志是否有错误，catalina.out。

### 2.3 安装管理后台

​	在开始之前，下载 `rasp-cloud.tar.gz`，并解压缩到本地。

​	（1）安装数据库

​	在 `1.0.0 RC1` 版本中，我们使用了 [ElasticSearch](https://www.elastic.co/) 和 [MongoDB](https://www.mongodb.com/) 两种数据库。前者用来存储报警和统计信息，后者用来存储应用、账号密码等信息。

​	目前我们对数据库的要求是，

​		MongoDB >= 3.6

​		ElasticSearch >= 5.6

​	（2）启动管理后台

​       首先，编辑 `conf/app.conf` 文件，将 `PanelServerURL`、`AgentServerURL` 里面的 `127.0.0.1` 替换为你的服务器IP或者域名。

​	`AgentServerURL` 一般填写内网地址，OpenRASP agent 会连接到这个地址；`PanelServerURL` 是管理后台地址，用于生成报警邮件里的链接，内外网地址均可。

```
PanelServerURL = http://127.0.0.1:8086
AgentServerURL = http://127.0.0.1:8086
```

​	然后，修正 ElasticSearch 和 MongoDB 两个服务器的地址。如果这两个服务器都安装在了本机，请跳过此步骤:

```
[prod]
EsAddr = http://127.0.0.1:9200
EsUser =
EsPwd =
MongoDBAddr = 127.0.0.1:27017
MongoDBUser =
MongoDBPwd =
```

​	最后，在终端里执行如下命令，启动后台服务器:

```
./rasp-cloud -d
```

​	启动成功后，可通过 `http://ip:8086` 访问管理后台。其中，后台用户名固定为 **openrasp**，初始密码为 **admin@123**。如果不能访问，请检查防火墙设置，或者检查 `logs/api/agent-cloud.log` 下面的错误信息。

​	（3）高级配置

​	1.负载均衡：

​	在实际场景中，我们需要部署多个服务器，才能处理来自数十万主机的连接。因此，我们将管理后台拆分为 `Agent 接口服务器 (AgentServer)` 和 `前端服务器 (PanelServer)`。前者用于跟agent通信，比如心跳、日志上传；后者用于展示数据、定时报警、修改配置，用户可访问这个后台来查看报警，只能启动一个。

​	启动一个前端服务器，

```
./rasp-cloud -type=panel -d
```

​	启动一个 agent 服务器（**使用同一份 conf/app.conf 配置文件即可**）

```
./rasp-cloud -type=agent -d
```

​	由于前端界面上只展示了一个 AgentServerURL 地址，所以当你安装 RASP agent 时，需要手动修正 `-backendurl` 或者 `--backend-url` 参数。

​	2.后台运维：

​	2.1 升级后台

请登录到每一台部署了 rasp-cloud 的主机，并按照如下步骤进行升级:

1. 备份原来的配置文件，`conf/app.conf`

2. 下载新的安装包，解压缩到相同目录

3. 检查是否需要更新配置文件

4. 执行命令 `ps aux | grep rasp-cloud` 找到后台进程 PID

5. 对上述进程发出 HUP 信号， `kill -HUP $PID`

6. 检查后台是否可以正常访问

   2.2 找回密码

​       请登录到后台所在的主机，执行如下命令，根据提示重置密码。目前密码强度要求是 `8-50` 位，必须包含数字和字母。

```
./rasp-cloud -type=reset
```

​	2.3 查看后台版本

​	请登录到后台所在的主机，执行如下命令即可:

```
%> ./rasp-cloud -version
```

​	2.4 定期备份

​	MongoDB 需要备份 `openrasp` 数据库，可以使用 mongodump + mongorestore 实现；ElasticSearch 需要备份如下索引，可以用 snapshot 方式备份:

```
real-openrasp-report-data-{appid}
real-openrasp-attack-alarm-{appid}
real-openrasp-policy-alarm-{appid}
real-openrasp-error-alarm-{appid}
```

### 2.4 SIEM系统集成

​	OpenRASP 可以无缝集成自研的、开源的还是商业SIEM产品。

​	文件日志：

​	默认情况下，我们会打印 JSON 格式的报警日志、基线日志。你可以通过部署主机agent，来采集日志。目前开源的日志采集工具有 `Logstash`, `Flume`, `Filebeat` 等等。

​	Syslog TCP 方式：

​	由于单条报警日志大小超过了1KB，可以使用Syslog TCP 方式，直接将日志发送到指定的服务器。

​	值得注意的是，在PHP版本里，syslog 是阻塞发送的。如果因为网络问题，导致syslog远程服务器不可用，发生攻击时会阻塞当前请求。

​	管理后台推送：

​	当管理后台收到报警日志，可以立即将日志推送到指定的 HTTP/HTTPS 地址，直接在界面上配置即可。为了方便测试，还支持推送测试报警。

### 2.5 大规模部署

​	物理机部署：

​	目前只提供了 Java + Tomcat 环境的批量安装脚本，可在源代码仓库里找到:

+ [baidu/openrasp - rasp-install/remote/linux/batch-installer.sh](https://github.com/baidu/openrasp/blob/master/rasp-install/remote/linux/batch-installer.sh)

  容器化部署：

​        容器部署通常都需要定制。一般是修改 Dockerfile，并在发布镜像时集成 OpenRASP。在执行下面的命令之前，请先在管理后台获取 app_id/app_secret/backendurl 等参数的值。

​	（1）Java 版本

​	在 tomcat/springboot 等服务器启动之前，增加 ADD + RUN 命令，

```
ADD https://packages.baidu.com/app/openrasp/rasp-java.tar.gz /tmp
RUN cd /tmp \
    && tar -xf rasp-java.tar.* \
    && /jdk/bin/java -jar rasp-*/RaspInstall.jar -install /tomcat/ -appid XXX -appsecret XXX -backendurl XXX \
    && rm -rf rasp-*
```

​	（2）php版本

​	在 apache/php-fpm 启动前增加如下命令

```
ADD https://packages.baidu.com/app/openrasp/rasp-php-linux.tar.bz2 /tmp/
RUN cd /tmp \
    && tar -xf rasp-php-linux.tar.bz2 \
    && php rasp-php-*/install.php -d /opt/rasp/ --backend-url XXX --app-id XXX --app-secret XXX \
    && rm -rf rasp-php*
```

### 2.6 卸载方法

​	如果你安装时使用了自动安装程序，那么卸载的时候你也应该使用自动卸载程序。

## 3. 服务配置 

### 3.1 日志说明

​	存储路径

​	OpenRASP 默认会开启文件日志，存储路径如下:

+ Java 版本: `<app_home>/rasp/logs/alarm/*.log*`
+ PHP 版本: `<openrasp_rootdir>/logs/alarm/*.log`

值得注意的是，Java 版本当前的报警没有日期，只有 rotate 之后才会有日期。

### 3.2 管理后台

#### 	3.2.1 添加主机

​	点击后台右上角的 **添加主机 -> 基础信息**，可获取安装 RASP agent 所需要的 `appid/appsecret/backendurl`三个关键字段:

![1553755125636](E:/GitHub/work/typora-user-images/1553755125636.png)

#### 	3.2.2 添加白名单

​	在 **系统设置 -> 黑白名单 -> 添加** 下面，可以增加检测点白名单。以 phpMyAdmin 应用为例，如果我们不想对 `10.10.50.52/phpMyAdmin/` 这个URL进行SQL注入检测，可以参考下图配置，点击保存后生效。

![1553755300684](E:/GitHub/work/typora-user-images/1553755300684.png)

#### 	3.2.3 修改登录密码

在 **系统设置 -> 登录认证 -> 修改登录密码** 下面，可以设置新的后台登录密码。目前我们不支持多用户模式，未来会逐步完善。对于新密码，我们的要求是 8-50 位，且同时包含数字和字符，不符合规范的密码会被拒绝。

![1553755400558](E:/GitHub/work/typora-user-images/1553755400558.png)

#### 	3.2.4 配置邮件报警

​	在界面配置即可。一般情况下，端口写 `25` 即可，如果服务器支持 STARTTLS，会自动开启加密。如果你使用 465 端口，才需要勾选强制TLS。

![1553755573864](E:/GitHub/work/typora-user-images/1553755573864.png)

