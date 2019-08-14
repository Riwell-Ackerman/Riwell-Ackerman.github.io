---
title: Linux学习笔记06
date: 2019-08-11 18:25:06
categories: 旧时光
tags: Linux
---
一、DNS概念：Domain Name System（域名系统）
是互联网上作为域名和IP地址相互映射的一个分布式数据库
二、DNS功能：
完成IP地址和域名之间的一个映射
三、DNS分类：
1、静态映射：
每台设备上都配置主机到IP地址的映射，各设备独立维护自己的映射表，而且只供本设备使用
2、动态映射：
建立一套域名解析系统（DNS），只在专门的DNS上配置主机到IP地址的映射，网络上需要使用主机名通信的设备，首先需要到DNS服务器查询主机所对应的IP地址
四、DNS数据库层次结构：
上级只知道直接下属的存在 
五、DNS查询方式：
1、递归查询：不知道的服务器会代替帮你查询
在该模式下DNS 服务器接收到客户机（也可能是DNS服务器）请求，必须使用一个准确的查询结果回复客户机。如果DNS 服务器没有存储查询DNS 信息，那么该服务器会代替询问其他服务器，该过程反复，直到得到解析结果，再一步步返回给源客户机。
2、迭代查询：服务器不知道，但是会告诉你你去询问谁
DNS 服务器会向客户机提供其他能够解析查询请求的DNS 服务器地址，当客户机发送查询请求时，DNS 服务器并不直接回复查询结果，而是告诉客户机另一台DNS 服务器地址，客户机再向这台DNS 服务器提交请求，依次循环直到返回查询的结果为止。这个查询过程可能会由你的本地DNS服务器帮你完成。
六、DNS服务器的类型：
1、主DNS:可以完成数据的修改   2、   辅助DNS:辅助DNS服务器向主DNS服务器请求数据更改        serial number:请求序列号
     refresh:请求更新时间

     retry:请求重试时间(该时间一定小于更新时间)

     expire:过期时间

     minittl:否定答案的缓存时间

     如果在expire(过期时间)内,主DNS依然没有响应辅助DNS请求,辅助DNS也将停止工作

3、缓存DNS服务器:只做区域的高速DNS缓存查询

DNS转发器:不负责查询,只负责转发

时间单位: D(天) W(周) H(小时) 默认的时间单位是秒

六、DNS解析记录

  DNS主要资源记录类型(RRT:Resource Record Type)

 1、A记录:正向解析记录,把FQDN解析为IP地址

www.baidu.com. 600 IN A 192.168.1.1

    或简写为

    www  600 IN A 192.168.1.1

 2、PRT记录(Pointer):反正指针,把IP地址解析成FQDN

    192.168.1.1 600 IN PTR www.baidu.com.

    或简写为:

    1  600 IN PRT www.baidu.com.

  3、MX(Mail Exchanger):邮件交换记录    abc@baidu.com

    优先级:0-99,数字越小优先级越高

baidu.com.  600 IN MX 10 mail.baidu.com.

baidu.com.  600 IN MX 20 mail2.baidu.com.

mail.baidu.com.    600 IN A 192.168.1.2

mail2.baidu.com.    600 IN A 192.168.1.3

  4、NS记录(Name Server):名称服务器,指明该区域中哪台主机是DNS服务器(通常位于DNS服务器区域数据库中第二行)

baidu.com. 600 IN NS ns1.baidu.com.

baidu.com. 600 IN NS ns2.baidu.com.

baidu.com. 600 IN NS ns3.baidu.com.

ns1.baidu.com. 600 IN A 192.168.10.10

ns2.baidu.com. 600 IN A 192.168.10.20

ns3.baidu.com. 600 IN A 192.168.10.30

 5、SOA记录(start of authority):起始授权机构,指明该区域中哪台DNS是主DNS服务器,必须位于区域数据库文件中的第一行

baidu.com. 600 IN SOA ns1.baidu.com. admin.baidu.com. (

                                                1000 ; serial number

                                                300  ; refresh

                                                200  ; retry

                                                500  ; expire

                                                30   ; minittl

                                                    )

  6、CNAME记录:(Canonical Name):别名

www.baidu.com. 600 IN A 192.168.10.1

www2.baidu.com. 600 IN CNAME www.baidu.com.

七、区域文件格式:

名称(name) 缓存时间(TTL:可省略) IN(intenet) RRT(资源类型) 数据(value)

DNS正向区域数据库:

baidu.com. 600 IN SOA ns1.baidu.com. admin.baidu.com. (    //SOA记录

                                                1000 ; serial number

                                                300  ; refresh

                                                200  ; retry

                                                500  ; expire

                                                30   ; minittl

                                                    )

baidu.com. 600 IN NS ns1.baidu.com.    //NS记录

baidu.com. 600 IN NS ns2.baidu.com.

ns1.baidu.com. 600 IN A 192.168.10.3    //A记录

ns2.baidu.com. 600 IN A 192.168.10.4

www.baidu.com. 600 IN A 192.168.10.1    

ftp.baidu.com. 600 IN A 192.168.10.2

mail.baidu.com. 600 IN A 192.168.10.5

baidu.com.  600 IN MX 10 mail.baidu.com.    //MX记录

DNS反向区域数据库:(10.168.192.in-addr.apra)

baidu.com. 600 IN SOA ns1.baidu.com. admin.baidu.com. (

                                                1000 ; serial number

                                                1H  ; refresh

                                                200  ; retry

                                                500  ; expire

                                                30   ; minittl

                                                    )

baidu.com. 600 IN NS ns1.baidu.com.

baidu.com. 600 IN NS ns2.baidu.com.

    3.10.168.192.in-addr.arpa 600 IN PTR ns1.baidu.com.

    4.10.168.192.in-addr.arpa 600 IN PTR ns1.baidu.com.

    1.10.168.192.in-addr.arpa 600 IN PTR www.baidu.com.

    2.10.168.192.in-addr.arpa 600 IN PTR ftp.baidu.com.

    5.10.168.192.in-addr.arpa 600 IN PTR mail.baidu.com.

baidu.com. 600 IN MX 10 mail.baidu.com.
