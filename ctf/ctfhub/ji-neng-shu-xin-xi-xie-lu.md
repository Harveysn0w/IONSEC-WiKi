---
description: Web技能树·基础·信息泄露
---

# 技能树·信息泄露

![image-20210303132247383](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303132247383.png)

## 1、目录遍历

![image-20210207000954390](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210207000954390.png)

点击后自动跳转~

![image-20210207001020638](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210207001020638.png)

既然说目录遍历，那就挨个点呗~

![image-20210207001934347](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210207001934347.png)

最终，在4/1里面找到了flag~

```text
ctfhub{846fed1d969138fefc5736dd}
```

## 2、PHPINFO

![image-20210303132417852](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303132417852.png)

![image-20210303132437874](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303132437874.png)

点击后显示phpinfo信息

![image-20210303132454472](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303132454472.png)

翻一翻就出来了

![image-20210303133016031](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303133016031.png)

```text
ctfhub{36ded0689d65ffeb4720296b}
```

浅谈ctf中phpinfo需要关注的点~

```text
https://xz.aliyun.com/t/6131
```

## 3、备份文件下载

![image-20210303133213543](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303133213543.png)

### 3.1 网站源码

![image-20210303133504097](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303133504097.png)

![image-20210303133525643](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303133525643.png)

扫描目录~

![image-20210303133803447](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303133803447.png)

将备份文件下载解压~

![image-20210303133909047](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303133909047.png)

![image-20210303134050030](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303134050030.png)

哇哦~

把文件名加到链接后面即可。

![image-20210303134146867](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303134146867.png)

```text
ctfhub{2ba7d229941e1f0856eb4c3f}
```

### 3.2 Bak文件

![image-20210303134726608](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303134726608.png)

扫描目录~

![image-20210303135014834](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303135014834.png)

找到备份文件~

![image-20210303135137380](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303135137380.png)

![image-20210303140639258](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303140639258.png)

```text
ctfhub{9fc70eb46a40e07bf75da89d}
```

### 3.3 vim缓存

![image-20210303140909896](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303140909896.png)

![image-20210303140923689](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303140923689.png)

```text
非正常关闭vim编辑器时会生成一个.swp文件
在使用vim时会创建临时缓存文件，关闭vim时缓存文件则会被删除，当vim异常退出后，因为未处理缓存文件，导致可以通过缓存文件恢复原始文件内容。
以 index.php 为例：第一次产生的交换文件名为 .index.php.swp
再次意外退出后，将会产生名为 .index.php.swo 的交换文件
第三次产生的交换文件则为 .index.php.swn。
```

由说明可知，flag在index.php源码中，那么备份文件应该是index.php.swp

我们访问链接，下载文件~

![image-20210303141931500](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303141931500.png)

可以使用vim -r {your file name} 命令来恢复文件

![image-20210303142531986](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303142531986.png)

![image-20210303142509921](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303142509921.png)

```text
ctfhub{32ed877b1b7439359d101e38}
```

### 3.4 .DS\_Store

```text
.DS_Store 文件利用 
.DS_Store 是 Mac OS 保存文件夹的自定义属性的隐藏文件。通过.DS_Store可以知道这个目录里面所有文件的清单。
```

![image-20210303164019908](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303164019908.png)

我们直接访问~下载了备份文件

![image-20210303164117757](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303164117757.png)

![image-20210303164127954](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303164127954.png)

在linux中直接cat文件就可以

![image-20210303164153764](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303164153764.png)

发现一个txt文件。打开~

![image-20210303164303105](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303164303105.png)

```text
ctfhub{4f97a11fe13ffd1b45ba37cc}
```

当然也可以用脚本：[https://github.com/gehaxelt/Python-dsstore](https://github.com/gehaxelt/Python-dsstore)

![image-20210303164936728](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210303164936728.png)

## 4、git泄露

### 1.Log

![image-20210422223619385](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422223619.png)

![image-20210422223754574](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422223754.png)

用dirsearch扫描一遍，接着使用githack将泄露文件搞出来。

![84C15A503A8EAF75D7561CE3C197495F](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422225954.jpg)

cd进入dist内的源码文件夹。

![image-20210422225608431](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422225608.png)

查看还原的信息，使用git log查看历史记录。

![image-20210422225548051](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422225548.png)

![28EDA5D23B4A42840321ED22DC214086](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422225153.jpg)

当前所处的版本为 `remove flag`，flag 在 `add flag` 这次提交中。与 add flag 这次提交进行比对。

命令如下

```text
git diff 84229b7a26796f3d49632adf48de97293b86fa72
```

![image-20210422225904370](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422225904.png)

![image-20210422225815994](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422225816.png)

```text
ctfhub{faa0e34abccd877fb400f258}
```

### 2.Stash

![image-20210422230502210](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422230502.png)

![image-20210422230511334](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422230511.png)

老样子githack进行处理源码泄露

![image-20210422230625650](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422230625.png)

进入扫描后的源码路径文件夹

![image-20210422231411631](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422231411.png)

执行git stash list ：查看stash了哪些存储。

![image-20210422231511453](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422231511.png)

![image-20210422231445831](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422231445.png)

执行git stash pop

```text
恢复之前缓存的工作目录，将缓存堆栈中的对应stash删除，并将对应修改应用到当前的工作目录下,默认为第一个stash,即stash@{0}，如果要应用并删除其他stash，命令：git stash pop stash@{$num} ，比如应用并删除第二个：git stash pop stash@{1}）
```

![image-20210422231724372](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422231724.png)

![image-20210422231749521](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422231749.png)

此时，目录下生成了新的文件

![image-20210422231827416](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422231827.png)

我们查看一下。

![image-20210422231849286](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422231849.png)

```text
ctfhub{86f262aa484a9e1f1cf02124}
```

#### PS：其他方法

![image-20210422232004327](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422232004.png)

![image-20210422232147413](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210422232147.png)

#### git stash的作用

```text
git stash 的作用
git stash用于想要保存当前的修改,但是想回到之前最后一次提交的干净的工作仓库时进行的操作.git stash将本地的修改保存起来,并且将当前代码切换到HEAD提交上.

通过git stash存储的修改列表,可以通过git stash list查看.git stash show用于校验,git stash apply用于重新存储.直接执行git stash等同于git stash save.

最新的存储保存在refs/stash中.老的存储可以通过相关的参数获得,例如stash@{0}获取最新的存储,stash@{1}获取次新.stash@{2.hour.ago}获取两小时之前的.存储可以直接通过索引的位置来获得stash@{n}.

git stash的概念理解
git stash的每个存储单元在工作仓库中是通过commit的形式由树结构展示的.它的根节点是HEAD所在的提交
```

### 3.index

![image-20210207002535343](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210207002535343.png)

打开环境~

![image-20210207002608602](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210207002608602.png)

既然信息泄露，那就dirsearch扫一波目录~我们看到了，有一个git备份文件。

![image-20210207002956264](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210207002956264.png)

接着使用GitHack干一波~[http://challenge-85d0e3d83147bf8e.sandbox.ctfhub.com/.git/](http://challenge-85d0e3d83147bf8e.sandbox.ctfhub.com/.git/)

![image-20210207003036501](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210207003036501.png)

搞定，回到GitHack的dist目录中可以看到扫描结果~

![image-20210207003128888](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210207003128888.png)

打开txt文件，发现flag~

```text
ctfhub{7fdfed11840917ea0d364e4c}
```

## 5、SVN泄露

![image-20210423230334027](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423230334.png)

![image-20210423230348313](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423230348.png)

提示为SVN信息泄露，flag在服务端旧版本中。首先进行手工判断是否存在/.svn 首先访问.svn/entries，下载后得到：

![image-20210423231800365](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423231800.png)

显示明文文件名等信息，再下载/.svn/wc.db后不能打开。直接上工具：SvnExploit 首先对/.svn目录下文件进行扫描：

![50306A16-CE27-4244-AE61-BBE2489C1E3A](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423231918.png)

查看到存在flag\_1637310845.txt文件，猜测为flag文件，下载整套源码：

![FAE39F786BC92A53510141F8B35D79BF](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423232140.jpg)

在svnExploit文件夹下：

![image-20210423232258986](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423232259.png)

但只下载了当前版本的index.html源码，题目提示在就版本中，所以这里使用：dvcs-ripper：

![image-20210423232723279](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423232723.png)

已经将.svn文件下载到本地，查看其中目录：

![239C0C33-47E9-42E3-BEE7-B899C7066733](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423233100.png)

![9E203D811C91C998C1D24F7438545D43](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423233345.jpg)

![image-20210423233404538](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423233404.png)

```text
ctfhub{60535c066506b812e580db1a}
```

#### PS:参考

```text
http://www.cxyzjd.com/article/tempulcc/108959901
https://www.cnblogs.com/batsing/p/svn-bug.html
```

#### SVN介绍

1）什么是SVN?

SVN是subversion的缩写，是一个开放源代码的版本控制系统，通过采用分支管理系统的高效管理，简而言之就是用于多个人共同开发同一个项目，实现共享资源，实现最终集中式的管理。

2）漏洞成因

在服务器上布署代码时。如果是使用 svn checkout 功能来更新代码，而没有配置好目录访问权限，则会存在此漏洞。黑客利用此漏洞，可以下载整套网站的源代码。 在使用SVN管理本地代码过程中，会自动生成一个隐藏文件夹，其中包含重要的源代码信息。但一些网站管理员在发布代码时，不愿意使用‘导出’功能，而是直接复制代码文件夹到WEB服务器上，这就使隐藏文件夹被暴露于外网环境，这使得渗透工程师可以借助其中包含版本信息追踪的网站文件，逐步摸清站点结构。

3）漏洞修复

方案一、不要使用svn checkout和svn up更新服务器上的代码，使用svn export（导出）功能代替。

方案二、服务器软件（Nginx、apache、tomcat、IIS等）设置目录权限，禁止访问.svn和.git目录，下面示范为禁止访问点号开头的目录

Nginx的vhosts中配置

```bash
location ~ (/\.){

    deny all;
}
```

Apache的vhosts中配置

```bash
<Directory ~ "/\.">
    Deny from all
</Directory>
```

#### svn源码泄露漏洞（git同理）

在服务器上布署代码时。如果是使用 svn checkout 功能来更新代码，而没有配置好目录访问权限，则会存在此漏洞。黑客利用此漏洞，可以下载整套网站的源代码。

一、.svn 目录

使用svn checkout后，项目目录下会生成隐藏的.svn文件夹（Linux上用ls命令看不到，要用ls -al命令）。

svn1.6及以前版本会在项目的每个文件夹下都生成一个.svn文件夹，里面包含了所有文件的备份，文件名为 .svn/text-base/文件名.svn-base

svn1.7及以后版本则只在项目根目录生成一个.svn文件夹，里面的pristine文件夹里包含了整个项目的所有文件备份

下面我以svn1.7及之后版本为例，讲解如何利用此漏洞下载整个网站源代码

二、分析.svn目录内容

![image-20210423233715897](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423233715.png)

我们看到的是一个名为 wc.db 的文件，用文本编辑器打开看到第一行有写 SQLite format 3 ，可以知道，这是一个SQLite数据库的文件，后面包含的信息，文本编辑器就先不看了。我们下载一个正经的SQLite查看软件来慢慢看－－[SQLite Studio](https://sqlitestudio.pl/index.rvt?act=download) 。

在软件下载完之前，再看看此文件夹里的其他内容：

entries和format文件里面，只有个数字12，没什么参考意义；wc.db-journal文件是空的，也没什么价值；tmp目录里面也是空的；

pristine里面内容就多了，一堆00~ff的文件夹，每个文件夹里有若干个 .svn-base文件；用文本编辑器打开看一下，有些文件是代码，有些文件是乱码（大概是图片文件吧）。看来这个文件夹是整个项目文件的一份备份，只是一堆哈希过的文件名，似乎有点难下手啊。

毕竟是无规律的这一堆文件名，40位的哈希（36^40）这样的暴力穷举下载的话，即便是N多线程，时间成本上也是非常大的啊。而且得到是一堆无序文件，要整理成原来代码项目的样子，还得费好一阵功夫。

这样看来，是不是说.svn漏洞利用的价值很少？然而非也，少年，还记得刚才说的 wc.db 文件吗。现在SQLite Studio软件应该下载好了，我们就打开看看这里有什么来头吧

三、wc.db文件，有你想要的一切

用SQLiteStudio软件打开 wc.db文件，我们看到 NODES 表，看到 local relpath栏 和 checksum栏，明白了吗（滑稽.jpg）。checksum栏里的$sha1$后面的那串数字就是pristine文件夹里的那堆文件的文件名，pristine里的00~ff文件夹，其实是文件名的前两位，而local relpath就是原始的文件名。

现在，我们根据这个 wc.db 的NODES表，遍历这个表里的每一行，就可以下载到整个项目里的代码了，而且还能得到对应的真实文件名，可谓岂不快哉？

（下面截图为我自己的项目，仅作参考）

![B59639A8-3B54-4E5E-AF03-579874AC1E93](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210423233825.png)

除了NODES表以外，还可以看到一个 REPOSITORY表，里面存储了svn的项目路径和 uuid，如果没有做访问IP限制的话，你可以直接使用此信息取得此项目的SVN权限（下载、提交等）…

四、漏洞修复

方案一、不要使用svn checkout和svn up更新服务器上的代码，使用svn export（导出）功能代替。

方案二、服务器软件（Nginx、apache、tomcat、IIS等）设置目录权限，禁止访问.svn和.git目录，下面示范为禁止访问点号开头的目录

Nginx的vhosts中配置

```text
location ~ (/\.){
    deny all;
}
```

Apache的vhosts中配置

```text
<Directory ~ "/\.">
    Deny from all
</Directory>
```

不只svn，git或者其他版本管理软件也存在类似的问题。

## 6、HG泄露

同样Mercurial也是一个版本控制软件，只不过是轻量级的，具体区别感兴趣可以去查一查。

![image-20210424125836596](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424125836.png)

![image-20210424125850183](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424125850.png)

先用dirsearch扫描的话，是可以扫出来有.hg的目录的。

使用dvcs-ripper下载hg文件：`./rip-hg.pl -v -u http://challenge-c9173ec82c414081.sandbox.ctfhub.com:10080/.hg/`

![E539A33ACB81FC09C5DE0A371768CE40](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424130618.jpg)

进入下载的.hg目录中（图形界面ctrl+h可以显示隐藏文件），直接`grep -r flag *`搜索，发现flag的一点轨迹。

![63D07C91-0903-46B9-A078-9AD9A5B14C9B](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424130947.png)

访问flag.txt

![image-20210424131034963](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424131035.png)

```text
ctfhub{b38a76d98edd9c11a4d473ce}
```

