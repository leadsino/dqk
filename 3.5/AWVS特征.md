##### AWVS

​	WVS(Web Vulnerability Scanner)是一个自动化的Web应用程序安全测试工具，它可以扫描任何可通过Web浏览器访问的和遵循HTTP/HTTPS规则的Web站点和Web应用程序。适用于任何中小型和大型企业的内联网、外延网和面向客户、雇员、厂商和其它人员的Web网站。WVS可以通过检查SQL注入攻击漏洞、XSS跨站脚本攻击漏洞等漏洞来审核Web应用程序的安全性。

#####  	主要过程：

​	1、扫描配置以及目标信息探测

​		同一个ip不断用不同的端口尝试TCP连接 ，扫描器使用的端口：56XXX

​	2、爬虫——目标页面信息爬取（站点爬行）

​		(1).http部分请求资源的对象带有信息 /acunetix-wvs-XXXXX

![1](https://github.com/leadsino/dqk/blob/master/3.5/images/1.png?raw=true)

​		

​		(2).http首部带有特殊的路径名

![2](https://github.com/leadsino/dqk/blob/master/3.5/images/2.png?raw=true)

​	3、漏洞探测

​		http首部可能带有如下特征值

​		accept:可接受的响应内容类型（`Content-Types`）。

​		origin:标识跨域资源请求

![3](https://github.com/leadsino/dqk/blob/master/3.5/images/3.png?raw=true)

![4](https://github.com/leadsino/dqk/blob/master/3.5/images/5.png?raw=true)

![5](https://github.com/leadsino/dqk/blob/master/3.5/images/4.png?raw=true)

