---
title: Linux学习笔记07
date: 2019-08-11 18:25:07
categories: 旧时光
tags: Linux
---
shell从广义上分为两类：

GUI：GNOME KDE XFACE等
CLI：sh csh bash
shell启动：当用户登录完成后，系统会自动启动shelll程序

进程：应用程序的副本，用PID区分（在系统中，进程只认为自己存在）

root student用户

shell：父shell程序和子shell程序（父子shell之间设置环境互相独立）

cat /etc/shells //查看支持哪些shell

yum -y install ksh csh

exit//退出当前shell

shell是一个程序

 

bash的特性：

命令历史history：Linux会自动记录系统过去执行的命令，并保存在内存的缓冲区中，
在每个用户的家目录下，有个隐藏文件.bash_history保存命令历史
-c：清空命令历史

-d：删除某一条命令历史

-w：将命令历史保存到某个文件中

变量：PATH命令路径变量

HISTSIZE：命令历史大小变量

echo HISTSIZE//查看HISTSIZE（命令历史大小）变量的值，默认1000

！n:执行命令历史中的第n个命令
！-n：倒数第n个命令
！Word：执行命令历史中最近一个匹配的命令
!!：执行上一条命令
!$：引用一个命令的最后一个参数
esc+.:引用一个命令的最后一个参数
管道，重定向

命令别名（alias定义的别名只在当前shell生效）

alias comm_alias=command

unalias comm_alias//取消别名

注意：执行alias命令时，command最好用‘’标识出来

alias cdnet='cd /etc/sysconfig/network-scripts'

 命令行编辑

ctrl+a：命令行行首
ctrl+e：命令行行尾
ctrl+u:删除光标到行首
ctrl+k:删除光标到行尾
ctrl+l:清屏
命令行展开

命令补齐：在PATH变量搜索命令并补齐（PATH变量必须正常）

路径和文件补齐：（在系统路径中查找）

命令替换：把命令中的子命令替换为子命令执行结果的过程

$(command)或者`command`

echo "Dirctory is $(pwd)"

 “”：弱引用，实现变量的替换，将变量的名字编程变量值

‘’：强引用，不能实现变量的替换


命令行通配：globbing

*：匹配任意长度的任意字符

？：匹配任意单个字符

[]：匹配指定范围内的任意单个字符

[ab][a-m][a-Z][A-Z][a-zA-Z][0-9][a-zA-Z0-9]

[^]:匹配指定范围外的任意单个字符

[:space:]：表示空格

[:punct:]：表示所有标点

[:lower:]：表示所有小写字母

[:upper:]：表示所有大写字母

[:alpha:]：表示所有字母（包括大小写）

[:dgit:]：表示所有的数字

[:alnum:]：表示所有数字和大小写字母
