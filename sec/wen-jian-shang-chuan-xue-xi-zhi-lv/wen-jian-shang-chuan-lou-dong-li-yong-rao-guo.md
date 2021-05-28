# 文件上传漏洞利用绕过

## 前言

文件上传漏洞绕过学习笔记、

### 一、js绕过

![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925115612809.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center)

#### 1·禁用浏览器js脚本

使用浏览器：Firefox

```text
about:config
```

![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925114441436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center)

```text
双击javascript.enabled关闭
```

#### 2·Burp suite抓包绕过

将一句话的拓展名改为白名单内容，后用Burp suite抓包改文件后缀 ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925120118696.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center) ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925120135908.png#pic_center) 上传成功

### 二、type绕过

修改type值为白名单内部值

#### 1·修改包内的Content-Type值

截获![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925121017363.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center)

修改![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925121055152.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center) 成功绕过 ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925121317112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center) 右击图片地址就是上传位置

### 三、扩展名绕过

黑名单绕过，网站提示不允许上传php文件，尝试改变拓展名 ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925122408707.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center)

#### 1·修改拓展名

将后缀名改为Php，测试 ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925122545340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center) 上传成功

### 四、00截断绕过

白名单 ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925123114145.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center)

#### 1·00截断

先将文件后面加上白名单后缀，进行初步上传 ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925123549715.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center) 用burp抓包，将1.php.jpg改为1.php%00.jpg，然后右击选择Covert selection选项中的URL进行编码。 ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925123838343.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center) 放包 ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925123943249.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center) 上传成功

### 五、修改文件头绕过

判断网站上因该是有验证文件实际格式的脚本 ![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925124734872.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center)

#### 1·用winhex修改文件头

修改文件头，在最前方添加jpg的图片格式的文件头

```text
Value = FF D8 FF E0 00 10 4A 46 49 46
```

![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925130749602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center)

上传测试

![&#x5728;&#x8FD9;&#x91CC;&#x63D2;&#x5165;&#x56FE;&#x7247;&#x63CF;&#x8FF0;](https://img-blog.csdnimg.cn/20200925125321736.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lJR0FPWVU=,size_16,color_FFFFFF,t_70#pic_center) 上传成功

## 结语

文件上传的练习就到这里，有掌握了几种新方法，开心\(～￣▽￣\)～

