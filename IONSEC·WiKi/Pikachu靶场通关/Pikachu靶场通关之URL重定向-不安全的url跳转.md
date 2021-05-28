# Pikachu靶场通关之URL重定向-不安全的url跳转

## 1、漏洞概述

```
不安全的url跳转

不安全的url跳转问题可能发生在一切执行了url地址跳转的地方。
如果后端采用了前端传进来的(可能是用户传参,或者之前预埋在前端页面的url地址)参数作为了跳转的目的地,而又没有做判断的话
就可能发生"跳错对象"的问题。

url跳转比较直接的危害是:
-->钓鱼,既攻击者使用漏洞方的域名(比如一个比较出名的公司域名往往会让用户放心的点击)做掩盖,而最终跳转的确实钓鱼网站

这个漏洞比较简单,come on,来测一把!
```

## 2、靶场实战

![image-20210104135838483](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104135838483.png)

四个链接随便点击，发现第四个链接存在参数：

```
http://123.56.245.68:83/vul/urlredirect/urlredirect.php?url=i
```

于是构造payload：

```
http://123.56.245.68:83/vul/urlredirect/urlredirect.php?url=http://www.baidu.com
```

![image-20210105124603874](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105124603874.png)

完成跳转~访问上面的链接会跳转到百度。一般用户看域名先看前面，用户可能以为自己访问的是xxx网站，但此处的任意url跳转已经把用户重定向到其他钓鱼页面了。

## 3、后台代码审计

通过GET请求获取到前端传进来的URL然后判断了一下，如果不等于ｉ就直接跳转。

![image-20210104140720960](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104140720960.png)

















