# CTFHub·彩蛋

![image-20210425141715349](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425141715.png)

## 1、首页

![image-20210427140551810](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427140551.png)

本题需要爆破CTFHub域名,我没说使用subDomainsBrute进行爆破。win也可以使用：[FuzzDomain](https://github.com/Chora10/FuzzDomain)

![45EEB7BD-BBF9-4816-9997-8D810471FE66](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427140807.png)

![image-20210427203605916](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427203606.png)

这里我们挨个试一下，只有api.ctfhub.com可以打开

![image-20210427203657366](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427203657.png)

F12查看一下

![image-20210427203714682](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427203714.png)

```text
ctfhub{c18732f48a96c40d40a06e74b1305706}
```

## 2、公众号

![image-20210425142143682](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425142143.png)

```text
ctfhub{c461256ba542f478c9d8b3482c76c29a}
```

## 3、题目入口

随意打开一个环境，然后等待或者手动注销环境。

![image-20210427135519387](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427135519.png)

F12显示网页源代码~找到flag

![image-20210427135549236](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427135549.png)

```text
ctfhub{b644d27a30b450b2f170c4f19ef1dd85fb1efc5d}
```

## 4、Writeup

writeup从后面翻就看到了~

![image-20210425190751241](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425190751.png)

```text
ctfhub{0936de34af2a6dd91fbd88fead25fc877c8a4b1b}
```

## 5、工具

![image-20210425191029231](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425191029.png)

burp拦截一下就可以看到了。

![image-20210425191146777](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425191146.png)

```text
ctfhub{19d9098bcd2d4e77e2c60425b3d6abf63cd0744f}
```

## 6、赛事

![image-20210425191605098](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425191605.png)

```text
ctfhub{70e2bc7fde5462dd49b8242a7dde88d953a858f5}
```

## 7、真题

![image-20210425191447177](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425191447.png)

```text
ctfhub{e8c897ac0fd3b8b8771bcc84e79117e7}
```

## 8、投稿提交

![image-20210427203756911](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427203756.png)

根据题目，我们看到flag\[0:6\] = ctfhub 这个就是Python里面的切片，也就是0-6个字符是ctfhub

接下来继续寻找，去投稿页面，看到了一串base64字符，还是隐藏的

![image-20210427204115307](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427204115.png)

```text
ZmxhZyU1QjE4JTNBMjQlNUQlMjAlM0QlMjAlMjJlOGM0OWIlMjI=
解码
flag%5B18%3A24%5D%20%3D%20%22e8c49b%22
```

![image-20210427204253323](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427204253.png)

再次url解码

![image-20210427204343708](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427204343.png)

```text
flag[18:24] = "e8c49b"
```

这时候，在文章底部看到了6-12的信息

![image-20210427204023081](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427204023.png)

我们继续看下一篇文章，还是一个隐藏的字符串

![image-20210427204619593](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427204619.png)

```text
666c61675b32343a33305d203d202231313332623522
```

Hex转ASCII得到

![image-20210427204824487](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427204824.png)

```text
flag[24:30] = "1132b5"
```

接下来看到了12-18的信息

![image-20210427204524815](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427204524.png)

接下来，在投稿说明中，有一张图片，我们保存下来一探究竟~

![image-20210427205108051](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427205108.png)

![image-20210427205142141](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427205142.png)

```text
flag[30:36] = "15b652"
```

在 [题目环境提交说明](https://writeup.ctfhub.com/Other/提交说明/d7098951.html) 页面 6.5 邮件发送（隐藏的信息前面）有一张图片

![image-20210427205319286](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427205319.png)

下载图片后使用Notepad++或者010打开在末尾发现flag切片

![image-20210427205603088](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427205603.png)

```text
flag[36:42] = "a5f3a8"
```

在writeup投稿说明这里，有一个下载demo

![image-20210427205824070](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427205824.png)

下载后解压示例.zip 在files文件夹下发现 egg\_flag.txt

![image-20210427205857855](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427205857.png)

![image-20210427205907036](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427205907.png)

```text
flag[42:48] = aes_256_ecb_decode("c6e1d72b1102f9b96b20c1f00cc7a178d5b3f99193faaa60e53b45b9e644d782", key)

anonymous: what's the key ??? 
you_know_who: i don't know
anonymous: so what can i do?
you_know_who: maybe you can try [a-z]{6} or guess
anonymous: oh shit!
```

AES加密，key未知。参考wp后了解到key是ctfhub[AES在线解密](http://tool.chacuo.net/cryptaes)

![image-20210427210054232](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210427210054.png)

```text
62013}
```

```text
flag[0:6] = "ctfhub"
flag[6:12] = "{029e0"
flag[12:18] = "2eb3a1"
flag[18:24] = "e8c49b"
flag[24:30] = "1132b5"
flag[30:36] = "15b652"
flag[36:42] = "a5f3a8"
flag:[42:48] = "62013}"
```

```text
ctfhub{029e02eb3a1e8c49b1132b515b652a5f3a862013}
```

