---
title: SSRF小梳理
date: 2019-08-11 18:25:11
categories: 玩
tags: 网络安全
---
## 简介
SSRF（Server-Side Request Forgery:服务器端请求伪造）是一种由攻击者构造形成由服务端发起请求的一个安全漏洞。一般情况下，SSRF攻击的目标是从外网无法访问的内部系统。（正是因为它是由服务端发起的，所以它能够请求到与它相连而与外网隔离的内部系统）

SSRF 形成的原因大都是由于服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制。比如从指定URL地址获取网页文本内容，加载指定地址的图片，下载等等。利用的是服务端的请求伪造。ssrf是利用存在缺陷的web应用作为代理攻击远程和本地的服务器。

 

## 利用手段

1. 可以对外网、内网、本地进行端口扫描，某些情况下端口的Banner会回显出来（比如3306的）；

2. 攻击运行在内网或本地的有漏洞程序（比如溢出）；

3. 可以对内网Web应用进行指纹识别，原理是通过请求默认的文件得到特定的指纹；

4. 攻击内网或外网有漏洞的Web应用；

5. 使用file:///协议读取本地文件。

 

## 漏洞出现点 

1）分享：通过URL地址分享网页内容

2）转码服务

3）在线翻译

4）图片加载与下载：通过URL地址加载或下载图片

5）图片、文章收藏功能

6）未公开的api实现以及其他调用URL的功能

7）从URL关键字中寻找

share、wap、url、link、src、source、target、u、3g、display、sourceURl、imageURL、domain    



## 防御方法 

1,过滤返回信息，验证远程服务器对请求的响应是比较容易的方法。如果web应用是去获取某一种类型的文件。那么在把返回结果展示给用户之前先验证返回的信息是否符合标准。
2, 统一错误信息，避免用户可以根据错误信息来判断远端服务器的端口状态。
3,限制请求的端口为http常用的端口，比如，80,443,8080,8090。
4,黑名单内网ip。避免应用被用来获取获取内网数据，攻击内网。
5,禁用不需要的协议。仅仅允许http和https请求。可以防止类似于file:///,gopher://,ftp:// 等引起的问题。

参考: http://blog.csdn.net/u011066706/article/details/51175626

这里有个问题，对于内网如果成千上网的机器，如何做一个限定或过滤或者其他的解决手段？(源于阿里巴巴校招面试)

 

## 绕过方法 

1、@

http://abc@127.0.0.1
2、添加端口号

http://127.0.0.1:8080
3、短地址

http://dwz.cn/11SMa
4、可以指向任意ip的域名：xip.io

<pre> <strong> 10.0.0.1</strong>.xip.io   resolves to   10.0.0.1
  www.<strong>10.0.0.1</strong>.xip.io   resolves to   10.0.0.1
  mysite.<strong>10.0.0.1</strong>.xip.io   resolves to   10.0.0.1
  foo.bar.<strong>10.0.0.1</strong>.xip.io   resolves to   10.0.0.1
5、ip地址转换成进制来访问

115.239.210.26 ＝ 16373751032

## 参考资料

http://blog.csdn.net/u011066706/article/details/51175626

http://bobao.360.cn/learning/detail/240.html

http://www.cnblogs.com/s0ky1xd/p/5859049.html
