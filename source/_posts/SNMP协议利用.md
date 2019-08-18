---
title: SNMP协议利用
date: 2019-08-17 12:41:39
categories: 大Kali	
tags: 漏洞复现
---
## 一、安装snmp服务
![1](SNMP协议利用/1.png)
## 二、配置snmp服务
运行Services.msc
添加社区public，只读
![2](SNMP协议利用/2.png)
![3](SNMP协议利用/3.png)
![4](SNMP协议利用/4.png)
启动服务

## 三、在kali运行

Snmpwalk -c public -v 2c IP

即可查看目标主机的大部分信息。
![5](SNMP协议利用/5.png)
