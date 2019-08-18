---
title: Nginx服务器安全加固tips整理
date: 2019-08-11 18:25:10
categories: 旧时光
tags: 网络安全
---
公司各业务网站大多用到Nginx，花了点时间整理了一下Nginx服务器安全加固的各类tips。


## 默认配置文件和Nginx端口

/usr/local/nginx/conf/-Nginx配置文件目录，/usr/local/nginx/conf/nginx.conf是主配置文件

/usr/local/nginx/html/-默认网站文件位置

/usr/local/nginx/logs/-默认日志文件位置

Nginx HTTP默认端口：TCP 80

Nginx HTTPS默认端口：TCP 443

可以使用以下命令来测试Nginx配置文件准确性。

/usr/local/nginx/sbin/nginx -t

将会输出：

the configuration file /usr/local/nginx/conf/nginx.conf  syntax is OK

configuration file /usr/local/nginx/conf/nginx.conf test is successful

执行以下命令来重新加载配置文件：

/usr/local/nginx/sbin/nginx -s reload

执行以下命令来停止服务器：

/usr/local/nginx/sbin/nginx -s stop

 

## 通过分区挂在允许最少特权

服务器上的网页/html/php文件单独分区。例如，新建一个分区/dev/sda5（第一逻辑分区），并且挂载在/nginx。确保/nginx是以noexec，nodev and nosetuid的权限挂载。

例：

LABAL=/nginx/nginx ext3 defaults,nosuid,noexec,nodev 1 2

注意：需要使用fdisk和mkfs.ext3命令创建一个新分区。

 

## 配置/etc/sysctl.conf强化Linux安全

可以通过编辑/etc/sysctl.conf来控制和配置Linux内核、网络设置。

###### Avoid a smurf attack

net.ipv4.icmp_echo_ignore_broadcasts=1

###### Turn on protection for bad icmp error messages

net.ipv4.icmp_ignore_bogus_error_responses=1

###### Turn on syncookies for SYN flood attack protection

net.ipv4.tcp_syncookies=1

###### Turn on and log spoofed,source routed,and redirect packets

net.ipv4.conf.all.log_martians=1

net.ipv4.conf.default.log_martians=1

###### No source routed packets here

net.ipv4.conf.all.accept_source_route=0

net.ipv4.conf.default.accept_source_route=0

###### Make sure no one can alter the routing tables

net.ipv4.conf.all.accept_redirects=0

net.ipv4.conf.default.accept_redirects=0

net.ipv4.conf.all.secure_redirects=0

net.ipv4.conf.default.secure_redirects=0

###### Don't act as a router

net.ipv4.ip_forward=0

net.ipv4.conf.send_redirects=0

net.ipv4.conf.default.send_redirects=0

###### Turn on execshild

kernel.exec-shield=1

kernel.randomize_va_space=1

 

## 删除所有不需要的Nginx模块

需要直接通过编译Nginx源代码使模块数量最少化。通过限制只允许web服务器访问模块把风险降到最低。

可以只配置安装nginx所需要的模块。例如，禁用SSL和autoindex模块可以执行以下命令：

./configure -without-http_autoindex_module -without-http_ssl_module

make

make install

通过以下命令来查看当编辑nginx服务器时哪个模块能开启或关闭：

./configure -help | less

禁用你用不到的nginx模块。

 

## 使用mod_security（只适合后端Apache服务器）

mod_security为Apache提供一个应用程序集的防火墙，为后续Apache Web服务器安装mod_security，这会阻止很多注入式攻击。

 

安装SELinux策略以强化Nginx Web服务器

默认的SELinux不会保护Nginx Web服务器，但是你可以安装和编译保护软件。

1.安装编译SELnux所需要环境支持

yum -y install selinux-policy-targeted selinux-policy-devel

2.下载SELinux策略以强化Nginx Web服务器。

cd /opt

wget http://downloads.sourseforge.net/project/selinuxnginx/se-ngix_1_0_10.tar.gz?use_mirror=nchc&rsquo;

3.解压文件

tar -zxvf se-ngix_1_0_10.tar.gz

4.编译文件

cd se-ngix_1_0_10/nginx

make

rm tmp/nginx.mod.fc tmp/nginx.mod

5.安装生成的nginx.pp SELinux模块：

/usr/sbin/semodule -i nginx.pp

 

## 控制缓冲区溢出攻击

编辑nginx.conf，为所有客户端设置缓冲区的大小限制。

vi /usr/local/nginx/conf/nginx.conf

编辑和设置所有客户端缓冲区的大小限制如下：

###### Start: Size Limits & Buffer Overflows ##    //server上下文

client_body_buffer_size 1K;

client_header_buffer_size 1k;

client_max_body_size 1k;

large_client_header_buffers 2 1k;

###### END:Size Limits & Buffer Overflows ##

 

## 控制并发连接

可以使用NginxHttpLimitZone模块来限制指定的会话或者一个IP地址的特殊情况下的并发连接。编辑nginx.conf:

###### Directive describes the zone,in which the session states are stored i.e. stored in slimits.

###### 1m can handle 32000 sessions with 32 bytes/session,set to 5m x 32000 session

limit_zone slimits $binary_remote_addr 5m;

###### Control maximum number of simultaneous connections for one session i.e.

###### restricts the amount of connections from a single ip address

limit_conn slimits 5;

上面标示线至每个远程IP地址的客户端同时打开连接不能超过5个。

 

## 只允许我们的域名的访问

如果机器人只是随机扫描服务器的所有域名，可以允许配置的虚拟域或反向代理请求，以拒绝这个请求。

###### Only requests to our Host are allowed i.e. xxx.in,images.xxx.in and www.xxx.in

if($host !~ ^(xxx.in|www.xxx.in|images.xxx.in)$){

retrun 444;

}

###### 

 

## 限制可用的请求方法

GET和POST是最常用的方法。Web服务器的方法被定义在RFC 2616。如果Web服务器不要求启用所有可用的方法，它们应该被禁用。下面的指令将过滤只允许GET,HEAD和POST方法：

###### Only allow these request methods

if($request_method !~ ^(GET|HEAD|POST)$){

retrun 444;

}

###### Do not accept DELETE,SEARCH and other methods

 

## 如何拒绝一些User-Agents？

###### Block download agents

if ($http_user_agent ~* LWP::Simple|BBBike|wget){

retrun 403;

}

###### 

组织Soso和有道的机器人：

###### Block some robots 

if ($http_user_agent ~* Sosospider|Yodaobot){

retrun 403;

}

 

## 目录限制

可以对指定的目录设置访问权限。所有的网站目录应该一一配置，只允许必须的目录访问权限。

可以通过IP地址来限制访问目录/admin/:

location /docs/ {

###### block one workstation

deny 192.168.1.1;

###### allow anyone in 192.168.1.0/24

allow 192.168.1.0/24;

###### drop rest of the world

deny all;

}

 

## 通过密码保护目录

首先创建密码文件并增加“user”用户：

mkdir /usr/local/nginx/conf/.htpasswd/

htpasswd -c /usr/local/nginx/conf/.htpasswd/passwd user

编辑nginx.conf,加入需要保护的目录：

###### Password Protect /personal-images/ and /delta/ directories###

location ~ /(personal-images/./delta/.){

auth_basic "Restricted";

auth_basic_user_file /usr/local/nginx/conf/.htpasswd/passwd;

}

密码文件生成后，也可以用以下的命令来增加允许访问的用户：

htpasswd -s /usr/local/nginx/conf/.htpasswd/passwd userName

 

## Nginx SSL配置

HTTP是一个纯文本协议，它是开放的被动监测。应使用SSL来加密你的用户内容。

创建SSL证书

执行以下命令：

cd /usr/local/nginx/conf

openssl genrsa -des3 -out server.key 1024

openssl req -new -key server.key -out server.csr

cp server.key server.key.org

openssl rsa -in server.key.org -out server.key

openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt

编辑nginx.conf并更新：

server{

server_name example.com;

listen 443;

ssl on;

ssl_certificate /user/local/nginx/conf/server.crt;

ssl_certificate_key /usr/local/nginx/conf/server.key;

access_log /usr/local/nginx/logs/ssl.access.log;

error_log /usr/local/nginx/logs/ssl.error.log;

}

重启nginx：

/usr/local/nginx/sbin/nginx -s reload

 

## 在防火墙级限制每个IP的连接数

网络服务器必须监视连接和每秒连接限制。PF和Iptables都能够在进入nginx服务器之前阻止最终用户的访问。
