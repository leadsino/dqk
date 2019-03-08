#### 1. Web应用扫描器简介

##### ​	1.1 什么是Web漏洞扫描器

​	Web应用扫描器是一种对目标站点持续发送http（s）请求，通过对服务器响应进行分析，从而发现被扫描站点存在的安全风险以及漏洞的工具。

​	一个综合的业务系统存在着大量的Web页面和资源，以简单的登陆界面为例，当用户输入用户名和密码提交时，业务系统需要对其参数进行验证，在系统开发安全意识不足时，这个验证过程就可能存在一定的安全风险。当业务系统的逻辑越复杂，内容越丰富，存在安全风险的几率就越高。若攻击者通过Web应用扫描器进行扫描，业务系统的安全问题会很快暴露，并被攻击者所利用 ，从而对业务系统不同程度的侵害。

##### 	1.2 Web应用扫描器的工作原理

​	Web应用扫描器扫描站点一般分为三个阶段。

​	第一阶段：站点爬行

​	扫描器通过爬虫爬取目标站点的所有链接以及页面中的所有资源，以获取整个站点的层次结构。这一过程可以分为两步：（1）网络访问。（2）收集链接（url）。

​	在扫描器进行对特定站点进行网络访问时，可以伪装自身的请求信息，cookie信息，在得到站点响应后，可以从html，html注释等静态资源以及通过webkit从DOM树，JS等动态资源中收集静态或动态的链接。

​	第二阶段：探测点获取

​	探测点由扫描器自定义的规则库所设置，常用的探测点包括get请求中的url参数，cookie值，请求头部，响应体等。探测点将用于下一步的漏洞探测。

​	第三阶段：漏洞检测

​	扫描器通过对探测点注入攻击代码来检测是否存在着相应的安全漏洞。在攻击中可能会发现新的url，此时再重复扫描过程，直至扫描结束。常见的攻击类型包括：SQL注入攻击，XSS跨站脚本攻击，CSRF跨站请求伪造，弱口令检测，文件上传攻击，网络脚本攻击等。

![1](https://github.com/leadsino/dqk/blob/master/3.7/images/1.png?raw=true)

##### ​1.3 扫描器的常见特征

​	扫描器对站点进行扫描与正常用户访问有何区别?

​	本文主要总结了以下几点：

​	（1）扫描器自带指纹

​	市场上存在一系列开源或者商用的Web应用扫描器。商用产品如Acunetix Web Vulnerability Scanner，AppScan，绿盟极光等，开源产品如Nikto，Paros proxy，X-scan等，每种扫描器的特点或许不同，但其主要扫描原理是相同的，并且开源产品往往不如商用版功能全面。目前汉武实验室暂时针对市面上最常用的AVWS，AppScan，绿盟极光进行相应的研究。

​	Awvs（Acunetix Web Vulnerability Scanner ）对目标站点进行扫描时，往往在http(s)请求中会带有其特征的指纹。

​	Awvs在请求url、header、body中往往会带有自身的特征信息。

```http
（1）Url:
acunetix-wvs-test-for-some-inexistent-file
by_wvs
acunetix_wvs_security_test
acunetix
acunetix_wvs
acunetix_test
（2）Headers:
Accept:acunetix/wvs
Accept-Language: acunetix_wvs_security_test
Acunetix-Aspect:
Acunetix-Aspect-Password:
Acunetix-Aspect-Queries:
Client-IP: acunetix_wvs_security_test
Cookie: acunetix
Cookie: acunetixCookie
Cookie: acunetix_wvs_security_test
Host: acunetix_wvs_security_test
HTTP_AUTH_PASSWD: acunetix
Location: acunetix_wvs_security_test
Origin: $(@print(md5(acunetix_wvs_security_test)))
Referer: acunetix_wvs_security_test
User-Agent: acunetix_wvs_security_test
Via: acunetix_wvs_security_test
X-Forwarded-Host: acunetix_wvs_security_test
X-Forwarded-For: acunetix_wvs_security_test
（3）Body
acunetix
acunetix_wvs_security_test
```

​	Appscan也会在扫描发送http(s)请求时，在其中会带有其特征的指纹。

```http
（1）Url
Appscan
（2）Headers
Accept: Appscan
Content-Type: Appscan
Content-Type: AppScanHeader
User-Agent:Appscan
（3）body
Appscan
```

​	绿盟极光同样会在扫描发送http(s)请求时，在其中会带有其特征的指纹。

```http
（1）Url
nsfocus
（2） Headers
User-Agent: Rsas
```

​	（2）时间特征

​	对扫描器而言都具有一个相同的时间特征。当扫描器通过对目标站点不断发出http请求来进行扫描时，单位时间内同一个IP地址对目标站点发出请求是大大区别于正常用户访问的。

​	（3）行为特征

​	扫描器在扫描过程中会有站点爬行，探测点获取和漏洞检测三个步骤。

​	每个步骤都会有扫描器区别于正常用户访问的特征。

​	扫描器在进行站点爬行时，会对网页html（包括注释）中存在的所有链接加以访问，而一些暗链或是注释中的链接等是普通用户无法触发的。

​	扫描器在进行探测点获取时，会构造出各种特殊的网页请求，对站点存在的资源进行访问，如对某个动态资源（http://www.xxx.com/js/xx.js)，这也是区别于普通用户访问之处。另外扫描器构造的站点不一定是真实存在的，所以目标站点会返回的大量的404响应。

​	扫描器在进行漏洞检测时，会对各个探测点注入攻击代码，此时发生的http(s)请求的url中会带有恶意代码。如当扫描器进行SQL注入攻击时，请求url往往会包括一些非法参数——id为负数，"$,-,!"等；当扫描器进行XSS攻击时，请求url通常会包含一些JS代码；当扫描器进行CSRF攻击时，往往请求url中会包含恶意的referer字段。

​	

#### 参考资料

1. WEB漏洞扫描原理 https://cloud.tencent.com/developer/news/175423

2. 常见扫描器或者自动化工具的特征（指纹）https://www.freebuf.com/column/156291.html

3. 十大漏洞扫描程序 https://blog.csdn.net/jackpk/article/details/38226487
4. 深入探讨Web拦截技术 https://www.waitalone.cn/web-scanner-interception-technology-depth.html

