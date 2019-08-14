---
title: Linux学习笔记03
date: 2019-08-11 18:25:03
categories: 旧时光
tags: Linux
---
一、Linux常见命令

file：查看文件类型（windows用扩展名识别文件类型）

语法：file [options] [args]

-b：显示结果时，不显示文件名
-c:显示执行file命令的执行过程（file是如何判断文件类型），便于排错或分析命令执行过程
-i：输出MIME类型的字符串
-L：查看软连接对应文件类型
-f：查看文件中文件名的类型
cat:一次性查看整个文件

语法：cat [option] [args]

从键盘输入创建一个新文件:

# cat >test.txt << EOF
> ajsncj//键盘输入
> ajsbja
> ajsbj
> EOF//键盘输入结束
向现有文件中追加数据：

# cat >>file_name <<EOF
> sdfsdf
> sdfsd
> EOF
把等多个文件内容合并到一个文件输出：

cat file1 file2 >file3

-n:显示文件内容是时显示行号（包括空行）

-b:显示文件内容是时显示行号（不包含空行）

-s:当文件中有多个空行时合并为一个空行

-E:每个结尾加$

 

cp:复制文件或目录

语法：cp [options] 

-f:强制复制

-r：递归复制目录

-s:为某个文件创造符号连接（软连接），而不是复制文件

-l:硬连接

-b：覆盖已有文件前，对目标文件进行备份

-p：复制文件时保留文件原有属性

-d：当复制软链接文件时，把目标文件或目录也会创建为软链接，并指向最原始的文件

-i 覆盖前询问

 

mv：move，西东或从命令文件和目录（目标和源文件同一目录是重命名）

-b：移动前先备份

-f：强制移动

-i：移动前询问

-t：将多个文件移动到同一个目录（目录在前，后面跟文件）

 

rm：remove删除

-i：删除前询问

-d：删除可能存在数据的目录

-f：强制删除

-v：显示命令执行过程

-r：递归删除整个目录

 

mkdir：创建空目录

-m：创建目录时同时设置权限

-p：递归创建目录

-v：显示创建目录的过程

 

touch：创建新文件或者修改时间戳

-a：只改变访问时间

-c:不创建文件

-d：使用指定时间戳创建文件，而不使用系统时间

-t：使用指定格式时间戳创建文件，而不使用系统时间

-f：解决与BSD系列UNIX系统兼容性问题

-m：只改变动时间

 

tree:查看目录树（=ls -R）

which:查看命令所在的路径

whereis：查看命令所在的路径，源代码文件，帮助信息所在的文件

-b：只查看文件所在的位置（等同于which命令）

-m：只查看帮助信息所在的位置

-s:支持看源代码所在的位置

 

二、用户管理

新建用户命令：useradd uername

/etc/default/useradd
# useradd defaults file
GROUP=100//
HOME=/home//
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
/etc/skel/
/etc/login.defs
创建用户时，可以设置新用户可登陆系统的实践范围

 

rm -f /var/run/yum.pid

passwd：设置密码

man 5 shadow

 

三、进程管理

进程的概念: 

    可以定义为：可并发执行的程序在一个数据集合上的运行过程。进程可以申请和拥有系统资源。传统操作系统中，进程是一个可以独立调度的基本单位（现代操作系统中为线程）。进程是一个动态的概念，它是“活的”，它有产生、运行、消亡的过程。进程与程序不是等同概念，程序是一个静态的概念，是指令和数据的集合，作为一种文件可长期存放在存储介质中。

 

进程的相关概念:父子关系 进程状态 优先级

    1、进程状态：

                task_uninterruptible:不可中断睡眠 mkdir:系统调用,硬件:I/O 1ms

                task_interruptible:可中断睡眠

                stopped:停止状态(不能被调入CPU运行)

                zombie:僵尸状态(结束父进程才能够退出当前进程的僵尸状态):进程运行完成后没有释放CPU和内存资源

    2、进程优先级:

                0-139数字,共140个优先级,数字越小,优先级越高

                0-99:系统分配优先级(用户不可控优先级)

                100-139:nice值 (用户可控优先级);-20到19;root用户可以任意调整nice值;普通用户只能调整0-19


高优先级进程:获取更多的CPU运行时长,更优先的让CPU执行

 

进程的特性：

    动态性

    并发性

    独立性

    异步性

 

CPU分环运行：

    0环：特权环，运行内核

    1-2环：库文件

    3环：进程环

 

task struct:进程的数据结构(存放在内存的内核空间中)，相当于进程控制块

task struct结构:状态 进程信息和内核栈 运行列表(指令等) MM(内存管理):memory mangement PID:进程ID号 群组信息 用户信息 文件系统 文件描述符等，同时也维护页表寄存器

 

进程内存结构:

    forbidden:内核空间

    program text:指令

    data+bss:全局变量

    heap:堆空间,进程运行时调用的数据等

    shared library:共享库

    stack:栈空间,局部变量

  

线程的概念:

    现代操作系统中，将传统的进程分离，即将资源申请和调度分开，进程作为资源的申请和拥有的单位，而进程作为调度的基本单位。thread:线程,轻量级进程

 

线程与进程的比较：

    调度

    并发性

    拥有资源

    系统开销

 

lock:死锁 自旋锁等

    由于临界区的存在导致了该概念的产生，临界区使用原则：忙则等待、有限等待、空闲让进、让权等待

 

进程管理命令：ps top pstree pgrep pidof kill killall bg fg jobs

    ps:process status:进程状态查看,运行ps那个时刻系统进程状态;BSD风格(不使用连字符),sys V风格(使用连字符),GNU长选项(使用两个连字符)

          a:显示和终端有关的进程

          u:显示进程用户的信息

          x:显示和终端无关的进程

          -e:显示所有进程信息

          -l:显示进程长格式

          -F:显示进程的完整信息

 

    ps aux:结果解析

              USER:该进程是有哪个用户发起的

              PPID:父进程号

              PID:进程号 /proc目录存放着进程相关的信息 在CentOS7:PID为1的进程是systemd 在CentOS6                 中PID为1的进程是init进程,PID为1的进程是系统中所有进程的父进程

              %CPU:该进程占用的CPU百分比

              %MEM:该进程占用内存的百分比

              VSZ:虚拟内存集(计算进程所占物理内存时,物理内存包括共享库的空间)

              RSS:常驻内存集(进程中不能被交换出去的数据)

              TTY:运行在哪个终端

              STAT:进程状态

                      D:不可中断睡眠状态

                      S:可中断睡眠状态

                      R:运行状态

                      T:停止状态

                      Z:僵尸状态

                      s:session leader:会话的领导者

                      <:优先级较高的进程(用户不可控)

                      N:优先级较低的进程(用户可控进程)

                      l:多线程进程

                      L:在内存中锁定分页

                      +:前台进程组中的进程

              START:进程启动时间

              TIME:该进程在CPU中实际运行的时长

              COMMAND:该进程命令名称

 

    ps -elF:结果解析

              PRI:系统分配的优先级(用户不可更改)

              NI:nice值，nice值的修改会影响pri的值，他们的总和不变

 

   调整进程的nice值:调整正在运行进程的nice

            renice [nice_num] PID

  指定进程运行的nice值(让某个命令以指定nice运行)

            nice -n [nice_num] COMMAND

 

    top:动态查看进程状态,每隔3秒刷新一次进程状态信息

           top结果解析:

                   第一行:当前系统时间 系统运行时长 登录系统的用户个数 过去1分钟,5分钟,15分钟平均负载

                   第二行:进程总数 正在运行的进程数 睡眠的进程数 停止状态进程数 僵尸状态进程数

                   第三行(按1键可以查看每个CPU的信息):CPU相关信息: us:用户进程所占CPU百分比 sy:系统进程所占CPU百分比 ni:nice所占CPU百分比 id:空闲进程所占CPU百分比 hi:硬件中断所占CPU百分比 si:软件中断所占CPU百分比 st:被hypervisor偷走的CPU的百分比

                   第四行:物理内存相关信息

                   第五行:交换分区相关信息

 

            top交互式命令:

                   M:按所占内存百分比进行排序

                   P:按所占CPU备份比进行排序

                   T:按进程运行时长进行排序

                   c:是否显示COMMAND列完整路径

                   l:是否显示top第一行

                   t:是否显示top中第二行和第三行

                   m:是否显示内存的相关信息

                   k:杀死某个进程

                   q:退出top进程

 

pstree:查看进程树(查看父子进程关系)

 

pgrep:查看某个进程PID号 pgrep process_name

 

pidof:查看某个进程PID号 pidof process_name

 

kill:杀死某个进程(向进程传递某个信号)

   -l:查看信号列表：

           1:SIGHUP:不停止服务的情况下,重新读取配置文件,并且应用配置文件中的设置

           2:SIGINT:Ctrl+c 中断某个进程

           9:SIGKILL:强制杀死某个进程

           15:SIGTERM:终止某个进程(默认信号)，相当于会有预警

    kill语法：

            kill -sig_num PID

            kill -sig_name PID

            kill %num:杀死后台的某个作业,%不能省略，num为使用jobs命令查看时的序号

 

    信号:进程间的通信(IPC)

            内存共享等

 

killall:杀死整个进程树,用法同kill

  

bg:把进程调入后台继续运行

fg:把后台的进程调入前台运行

    fg num：将后台序号为num的进程调入前台

jobs:查看后台运行的作业

   +:下一次将要运行的后台作业

 

前台-->后台:

   ctrl+z:把正在运行的进程调入后台,并停止运行

   &:启动时直接调入后台运行，末尾加上该符号

 

四、任务计划

任务计划:调度性的任务

        分为：

                一次性任务计划：at、batch

                周期性任务计划：cron、anacron

 

一次性任务计划:

（一）at命令

          1、at：设置系统在某一个时间点执行的任务,执行结果以邮件的方式发送给任务发起者

              依赖atd服务，所以需要：

              # systemctl start atd

              # systemctl enable atd

              # systemctl status atd

 

          2、语法:

                  # at data_time

                  at>at_command1   //输入需要执行的命令

                  at>at_command2

                  at>ctrl+d 保存退出

 

                  data_time:时间日期写法

                            绝对时间:

                                  HH:MM:具体时间点

                                  DD.MM.YY:天.月.年

                                  YY-MM-DD:年-月-日

                                  MM/DD/YY:月/日/年

                                  HH:MM YY-MM-DD:

                            相对时间:

                                  at now+3minutes:从任务计划创建开始计时,3分钟以后执行

  

         3、与at命令相关的文件：

                 Linux中设置at任务后,会在/var/spool/at/目录生成at任务计划的文件,按照文件名的排序顺序执行

                权限:

                /etc/at.allow:at任务计划的白名单,仅允许此文件中的用户执行at

                /etc/at.deny:at任务计划的黑名单,仅拒绝次文件中的用户执行at

                系统先查找/etc/at.allow,再去查找/etc/at.deny,如果两个文件都没有,可以执行

 

         4、at命令:设置at任务计划

              -l:查看系统中at任务列表,相当于atq

              -r:删除系统中的at任务列表,相当于atrm

              -d:删除系统中的at任务列表,相当于atrm

              -c:列出后续at任务的具体指令内容

 

 

（二）batch

         1、batch:用法和at相同,不过batch任务计划是在CPU空闲时执行（当CPU的平均负载小于0.8时，uptime:查看CPU的平均负载）

                依赖batchd服务，所以需要：

              # systemctl start batchd

              # systemctl enable batchd

              # systemctl status batchd

 

          2、语法：

            # batch data_time

             batch>batch_command1

             batch>batch_command2

             batch>ctrl+d 保存退出

  

              data_time:时间日期写法：

                绝对时间:

                      HH:MM:具体时间点

                      DD.MM.YY:天.月.年

                      YY-MM-DD:年-月-日

                      MM/DD/YY:月/日/年

                      HH:MM YY-MM-DD:

                相对时间:

                      at now+3minutes:从任务计划创建开始计时,3分钟以后执行

  

周期性任务计划:

  系统周期性任务:

        日志轮换:logrotate

        登录文件分析:logwatch任务

        创建locate数据库:updatedb

        创建man帮助信息数据库:CentOS6:makewathis  CentOS7:mandb

        RPM软件登录文件的创建

        与网络相关的分析行为:

  

  用户周期性任务:

      cron:设置周期任务计划

      anacron:当cron周期性任务由于某种原因没有执行,anacron重新执行cron任务

 

（一）cron

    1、cron：

             依赖crond服务，所以需要：

              # systemctl startcrond

              # systemctl enablecrond

              # systemctl statuscrond

 

    2、相关文件：

            权限控制文件：

                /etc/cron.allow和/etc/cron.deny

                /etc/cron.allow:白名单

                /etc/cron.deny:黑名单,默认情况下系统只保留/etc/cron.deny

 

           cron相关的配置文件:

              /etc/crontab:cron配置文件

              /etc/cron.d/:cron辅助配置文件目录

              /var/spool/cron/:cron任务计划的配置文件目录,用户设置完cron任务后,会生成以用户名名称的cron文件

 

            /etc/crontab文件解析:

                   SHELL=/bin/bash:cron任务计划执行使用的shell程序

                   PATH=/sbin:/bin:/usr/sbin:/usr/bin:cron任务计划命令搜索地址

                   MAILTO=root:cron任务计划执行结果发送给哪个用户

 

            /etc/daily/   /etc/hourly/   /etc/monthly/   /etc/weekly/，这些目录下存放了固定的周期性任务

            cron.hourly:cron工具去执行

            cron.daily,cron.weekly cron.monthly是由anacron工具去执行

 

    3、操作：

        crontab:设置/查看/删除等cron任务计划,执行结果以邮件方式发送给任务发起者

              -e:会通过文本格式建立，在文本中编辑cron任务内容，语法见下文

              -u:为指定用户设置cron任务计划(只有root用户能够使用)

              -l:查看cron任务列表

              -r:删除所有的cron列表

 

        设置任务语法:

          系统设置:

              分 时 日 月 周 用户 命令

          用户设置:

              分 时 日 月 周 命令

 

        时间取值范围:

              分钟:0-59

              小时:0-23

              日期:1-31

              月份:1-12或jan,feb,mar,apr ...

              周:0-7或sun,mon,tue,wed,thu,fri,sat,其中0和7都代表周日

        时间的特殊写法:

              通配:*代表任意时间  10 17 * * * cat /etc/fstab

              离散时间段:,(逗号)  5,15,30,45,56 17 * * * cat /etc/fstab

              连续时间段:-(中横线) 00 23 * * 1-5 cat /etc/fstab

              间隔时间段:*/n    */5 * * * * cat /etc/fstab    00 10 */5 * * cat /etc/fstab（分不能通配）

 

    4、cron设置任务计划时注意事项:

         CPU和内存资源集中使用时的问题:  

            # vim /etc/crontab

                  1,6,11,16,21,26,31,36,41,46,51,56 * * * * root COMMAND1

            取消一些不必要的输出:设置命令时可以重定向输出

            时间的检验:设置时间时,周与日月不可并存

            安全的检验:通过/var/log/cron的内容查看cron任务计划中是否有非法任务

            环境变量:系统命令查找和cron任务命令位置由$PATH变量决定,设置cron任务时,使用命令的绝对路径

  

（二）anacron

    1、anacron:当cron周期性任务由于某种原因（如关机）没有执行,anacron重新执行cron任务

 

    2、语法:

          anacron [options] [job]

               -s:依据时间记录文件去判断是否执行

               -f:强制执行

               -n:立刻运行未执行的任务,不需要延时

 

    3、anacron执行流程:

        1:由/etc/anacrontab分析到cron.daily天数为1天

        2:由/var/spool/anacron/cron.daily取出最近一次执行anacron时间戳

        3:由2步骤分析出的时间戳和cron任务进行比较,查看哪些cron任务超过1天时间没有执行

        4:准备执行指令,根据/etc/anacrontab的设置,将延时执行

        5:延时时间过后,执行指令
