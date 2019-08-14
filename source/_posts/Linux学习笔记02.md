---
title: Linux学习笔记02
date: 2019-08-11 18:25:02
categories: 旧时光
tags: Linux
---
如何高效获取命令帮助信息

一、

# echo $PATH
内核版本号：主.次.修订 

系统中：用户名UID（只认识，不认识名字）

root：密码

student：

3A认证：

认证机制authentication

   密码认证：符合复杂性

             字符长度（至少7位）

             不要使用易记

             定期更改

             重复密码的时间要长 

授权机制authorization

审计机制audition（日志）

 

Linux登录信息：/etc/issue

ls(list):列出目录

-l（--long）：以长格式显示

 第一位：文件类型 

环境变量：内存中的命名空间

PATH变量：系统的命令路径，以冒号隔开

查看PATH变量：# echo $PATH（严格区分大小写）

[root@iZ2zegxnkic14t6f90xge7Z ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin

如果一个命令在以上多个路径中存在，系统按从前往后的顺序查找，查找到后不会再去查找。

hash:查看命令缓存和命中率

 

二、

# type cd
cd is a shell builtin
# type ls
ls is aliased to `ls --color=auto'
# which ls
alias ls='ls --color=auto'
	/bin/ls
# ls --help
内部命令：help [内部命令]

外部命令：[外部命令] --help

man：帮助信息（通用）

man  [命令字]

enter/上下方向键：翻行

page up/down:翻页

/word:从上往下找

?word:从下往上找

q：退出

info：查看在线帮助，更注重于命令的历史，版权信息

man目录说明：（/usr/share/doc
）工作中查看详细信息

<>:不可省略

[]:可以省略

...:多选一

{}：分组，没什么用

User Commands：用户普通命令（有bin的都是普通用户可以用的）
System Calls：系统调用库
C Library Functions：库调用
Devices and Special Files：设备或特殊文件（硬件设备）（硬件设备存放在/dev/）
File Formats and Conventions：查看配置文件格式
Games et. Al.：游戏
Miscellanea：杂项
System Administration tools and Daemons：管理命令(/sbin /user/sbin /user/local/sbin)sbin:secret binary
whatis:查看命令所在的帮助信息目录（系统启动70分钟后才会生成数据库）

makewahtis初始化该命令

man使用说明：

NAME：显示命令名称

SYNOPSIS：语法格式

DESCRIPTION：命令或选项的详细说明

Exit status:退出状态码

AUTHOR：作者信息

REPORTING BUGS：发现bug反馈

COPYRIGHT：版权

SEE ALSO：

OPTIONS：详细用法

EXAMPLE：使用实例
 

man查看内部命令时，显示的是bash信息

 

cd：change directory（切换命令）

-：返回到上一次的工作路径

-username：
