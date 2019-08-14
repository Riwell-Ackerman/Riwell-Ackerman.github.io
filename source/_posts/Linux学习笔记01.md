---
title: Linux学习笔记01
date: 2019-08-11 18:25:01
categories: 旧时光
tags: Linux
---
一、LINUX文件类型

-：普通文件：

纯文本文件（ascll） 配置文件
二进制文件（binary file）：命令
数据格式文件（date）：/var/log/wtmp
d:目录文件

l：链接文件

设备文件（/dev）：

b：block块设备
c：character 字符集设备：一次性读取，按顺序读取
s：socket套接字文件：用在网络上的数据连接IP：PORT

p：管道文件：特殊的文件类型，解决多个应用程序同时访问一个文件造成的错误问题

 

file：查看Linux中的文件类型

语法：file [options] filename

Linux文件名限制：

单个文件或者目录不能超过255字符
文件名当中不能包含一些特殊字符
 

文件查找命令: which locate find

  which:查找命令字所在的位置

  locate:模糊匹配(只要包含关键字的文件都查找出来)

             不是实时的,基于数据库查找, updatedb升级locate数据库

             查找速度特别快

 

  find:查看文件或目录的工具

          特点：  实时查找

                      精确查找

                      遍历整个指定目录中的文件,速度很慢

                      支持多种查找条件(属主,属组,权限,文件名等)

    

        语法: find [find_path] [条件] [处理动作]

                   find_path:如果不写,默认在当前工作目录查找

                   条件:如果不写,会查找出该路径下所有的文件

                   处理动作:默认打印到屏幕上 print

 

        条件:(匹配标准)

           -name file_name:根据文件名进行查找,区分大小写

                    查找条件通配符:

                       *:通配任意个数的任意字符   # find /etc/ -name "pass*"  # find /etc/ -name "*pass"

                        ?:通配单个的任意字符       # find /etc/ -name "passw*"

           -iname file_name:根据文件名进行查找,不区分大小写

           -regex PATTERN:根据正则表达式的模式进行查找

           -user:通过属主进行查找

           -group:通过属组进程查找

           -uid UID:通过UID号进行查找

           -gid GID:通过GID号进行查找

           -nouser:查找没有属主的文件

           -nogroup:查找没有属组的文件

           -type:根据文件类型进行查找

                 -b:块设备

                 -c:字符设备

                 -d:目录

                 -f:文件

                 -p:命名管道

                 -s:套接字文件

                 -l:链接文件

           -size [+ | -] num [k|m|g]

                 -size 5k:精确匹配

                 +:大于

                 -:小于

 

        根据时间戳进行查找: # stat file_name  //查看文件的时间戳

            [+ | -] 也起作用

            -atime:时间单位为天,access时间

            -mtime:时间单位为天,modify时间

            -ctime:时间单位为天,change时间

            -mmin:时间单位为分钟,modify时间

            -amin:时间单位为分钟,access时间

            -cmin:时间单位为分钟,change时间

 

        根据权限查找:

         -perm MODE:

               MODE：num（如644）,精确匹配

               /MODE：/num任意一位匹配

               -MODE：-num文件权限能够完全包含此MODE时才能匹配

 

       条件组合:

             -a:同时满足多个条件,比如查找属主是tom并且权限为644的文件

             -o:满足一个条件即可,比如查找属主是tom或者权限为644的文件

             -not:不满足条件的被查找出来

 

       出来动作:

         -print:默认动作,默认查找结果打印到屏幕

         -ls:默认以类似于ls -l的形式显示结果的信息

         -ok COMMAND {} \;执行动作时提示用户是否执行,必须以\;结尾,{}代表文件名占位符

         -exec COMMAND {} \;执行动作时不提示用户直接执行,必须以\;结尾,{}代表文件名占位符

 

  

1、查找/var目录下属主为root并且属组为mail的所有文件；

find /var -user root -a -group mail

 

2、查找/usr目录下不属于root,bin,或student的文件；

find /usr -not -user root -a -not -user bin -a -not -user student

find /usr -not \( -user root -o -user bin -o -user student \)

 

3、查找/etc目录下最近一周内内容修改过且不属于root及student用户的文件；

find /etc -mtime -7 -not \ ( -user root -o -user student \)

find /etc -mtime -7 -not -user root -a -not -user student

 

4、查找当前系统上没有属主或属组且最近1天内曾被访问过的文件，并将其属主属组均修改为root；

find / \( -nouser -o -nogroup \) -a -atime -1 -exec chown root:root {} \;

 

5、查找/etc目录下大于1M的文件，并将其文件名写入/tmp/etc.largefiles文件中；

find /etc -size +1M >> /tmp/etc.largefiles

 

6、查找/etc目录下所有用户都没有写权限的文件，显示出其详细信息；

find /etc -not -perm /222 -ls    

 

二、权限:是用户对于系统资源及数据等操作的能力
 
三类用户权限:
属主:owner
属组:group
其他:other
 
-rw-r--r--:10位
第一位:文件类型
第2-4位:文件属主权限
第5-7位:文件属组权限
第8-10位:其他用户的权限
 
文件:
r(read):读权限,可以查看文件的内容,使用cat more等命令
w(write):写权限,可以修改文件的内容及删除文件
x(execute):执行权限,可执行文件或脚本
 
目录:
r:读权限,可以查看目录中的列表,执行ls命令
w:写权限,可以向目录中新建文件或目录等
x:执行权限,可以执行cd命令
 
改变权限的命令
chmod 改变文件或目录的权限
chmod 755 abc：赋予abc权限rwxr-xr-x
chmod u=rwx,g=rx,o=rx abc：同上u=用户权限，g=组权限，o=不同组其他用户权限
chmod u-x,g+w abc：给abc去除用户执行的权限，增加组写的权限
chmod a+r abc：给所有用户添加读的权限
 
 
 
改变所有者（chown）和用户组（chgrp）命令
chown xiaoming abc：改变abc的所有者为xiaoming
chgrp root abc：改变abc所属的组为root
chown root ./abc：改变abc这个目录的所有者是root
chown ‐R root ./abc：改变abc这个目录及其下面所有的文件和目录的所有者是root
 
 
umask:反掩码(在/etc/login.defs中定义)
rwx:7
文件默认:rw-rw-rw-:666 644=rw-r--r--新建文件时,权限掩码(666)减去反掩码(022)就是新建文件的权限(644)
目录默认:rwxrwxrwx:777 755=rwxr-xr-x，新建目录时,权限掩码(777)减去反掩码(022)就是新建目录的权限(755)
 
 
Linux隐藏权限:
lsattr:查看文件或目录的隐藏权限
 
chattr:修改文件的隐藏权限,/   /dev    /tmp    /var    /etc  目录不受chattr保护
语法:
chattr +-= [options] file_name
 
a:只能向文件中追加数据,不能删除
i:任何用户不能删除文件
A:不同步访问时间
+:添加相应权限
-:删除相应权限
=:设定相应权限
 
/etc/passwd /etc/shadow /etc/group
 
tom:/etc/passwd /etc/shadow /etc/group添加信息,新建宿主目录(修改宿主目录的归属)
 
echo "tom:x:1001:1001::/home/tom:/bin/bash" >> /etc/passwd
