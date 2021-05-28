# Web技能树-密码口令

![image-20210425111738403](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425111738.png)

## 1、弱口令

![image-20210425111838374](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425111838.png)

![image-20210425112021299](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425112021.png)

随意试了几次，密码出来了，admin-admin123

![image-20210425113632140](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425113632.png)

抓包，看看效果

![image-20210425112405360](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425112405.png)

接下来爆破密码

就看自己的字典喽~

```
ctfhub{1713bb1c0080f2a06316de98}
```

#### intruder模块介绍

```
positions 选择攻击模式

sniper 对变量依次进行暴力破解
需要字典：1个 【payloadset只可选1个】
变量数量：不限
加载顺序：将字典依次填入所有变量中

battering ram 对变量同时进行破解
需要字典：1个 【payloadset只可选1个】
变量数量：不限
加载顺序：将字典依次同时填入所有变量中

pitchfork 每一个变量标记对应一个字典，一一对应进行破解
需要字典：N个 【payloadset可以选择N个】
变量数量：N个【需要和字段数量相同】
加载顺序：字典和变量分别对应，同时填入对应变量中

cluster bomb 每个变量对应一个字典，并且进行交叉试破解，尝试各种组合
需要字典：N个 【payloadset可以选择N个】
变量数量：N个【需要和字段数量相同】
加载顺序：典和变量分别对应，分别填入对应变量中
payload数量： user字典数 X pass字段数
```

## 2、默认口令

![image-20210425113924785](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425113924.png)

![image-20210425114047293](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425114047.png)

题目是默认口令，我们去找这个产品的默认口令即可。
账号：eyougw 密码：admin@(eyou)   登录成功即可得到flag

![image-20210425114522112](https://gitee.com/Harveysn0w/mac-note_img/raw/master/20210425114522.png)

```
ctfhub{24da638f9d02fed3e8a67611}
```

#### 常见网络安全设备弱口令(默认口令)

| 设备                                  | 默认账号      | 默认密码                          |
| ------------------------------------- | ------------- | --------------------------------- |
| 深信服产品                            | sangfor       | sangfor sangfor@2018 sangfor@2019 |
| 深信服科技 AD                         |               | dlanrecover                       |
| 深信服负载均衡 AD 3.6                 | admin         | admin                             |
| 深信服WAC ( WNS V2.6)                 | admin         | admin                             |
| 深信服VPN                             | Admin         | Admin                             |
| 深信服ipsec-VPN (SSL 5.5)             | Admin         | Admin                             |
| 深信服AC6.0                           | admin         | admin                             |
| SANGFOR防火墙                         | admin         | sangfor                           |
| 深信服AF(NGAF V2.2)                   | admin         | sangfor                           |
| 深信服NGAF下一代应用防火墙(NGAF V4.3) | admin         | admin                             |
| 深信服AD3.9                           | admin         | admin                             |
| 深信服上网行为管理设备数据中心        | Admin         | 密码为空                          |
| SANGFOR_AD_v5.1                       | admin         | admin                             |
| 网御漏洞扫描系统                      | leadsec       | leadsec                           |
| 天阗入侵检测与管理系统 V7.0           | Admin         | venus70                           |
|                                       | Audit         | venus70                           |
|                                       | adm           | venus70                           |
| 天阗入侵检测与管理系统 V6.0           | Admin         | venus60                           |
|                                       | Audit         | venus60                           |
|                                       | adm           | venus60                           |
| 网御WAF集中控制中心(V3.0R5.0)         | admin         | leadsec.waf                       |
|                                       | audit         | leadsec.waf                       |
|                                       | adm           | leadsec.waf                       |
| 联想网御                              | administrator | administrator                     |
| 网御事件服务器                        | admin         | admin123                          |
| 联想网御防火墙PowerV                  | administrator | administrator                     |
| 联想网御入侵检测系统                  | lenovo        | default                           |
| 网络卫士入侵检测系统                  | admin         | talent                            |
| 网御入侵检测系统V3.2.72.0             | adm           | leadsec32                         |
|                                       | admin         | leadsec32                         |
| 联想网御入侵检测系统IDS               | root          | 111111                            |
|                                       | admin         | admin123                          |
| 科来网络回溯分析系统                  | csadmin       | colasoft                          |
| 中控考勤机web3.0                      | administrator | 123456                            |
| H3C iMC                               | admin         | admin                             |
| H3C SecPath系列                       | admin         | admin                             |
| H3C S5120-SI                          | test          | 123                               |
| H3C智能管理中心                       | admin         | admin                             |
| H3C ER3100                            | admin         | adminer3100                       |
| H3C ER3200                            | admin         | adminer3200                       |
| H3C ER3260                            | admin         | adminer3260                       |
| H3C                                   | admin         | adminer                           |
|                                       | admin         | admin                             |
|                                       | admin         | h3capadmin                        |
|                                       | h3c           | h3c                               |
| 360天擎                               | admin         | admin                             |
| 网神防火墙                            | firewall      | firewall                          |
| 天融信防火墙NGFW4000                  | superman      | talent                            |
| 黑盾防火墙                            | admin         | admin                             |
|                                       | rule          | abc123                            |
|                                       | audit         | abc123                            |
| 华为防火墙                            | telnetuser    | telnetpwd                         |
|                                       | ftpuser       | ftppwd                            |
| 方正防火墙                            | admin         | admin                             |
| 飞塔防火墙                            | admin         | 密码为空                          |
| Juniper_SSG__5防火墙                  | netscreen     | netscreen                         |
| 中新金盾硬件防火墙                    | admin         | 123                               |
| kill防火墙(冠群金辰)                  | admin         | sys123                            |
| 天清汉马USG防火墙                     | admin         | venus.fw                          |
|                                       | Audit         | venus.audit                       |
|                                       | useradmin     | venus.user                        |
| 阿姆瑞特防火墙                        | admin         | manager                           |
| 山石网科                              | hillstone     | hillstone                         |
| 绿盟安全审计系统                      | weboper       | weboper                           |
|                                       | webaudit      | webaudit                          |
|                                       | conadmin      | conadmin                          |
|                                       | admin         | admin                             |
|                                       | shell         | shell                             |
| 绿盟产品                              |               | nsfocus123                        |
| TopAudit日志审计系统                  | superman      | talent                            |
| LogBase日志管理综合审计系统           | admin         | safetybase                        |
| 网神SecFox运维安全管理与审计系统      | admin         | !1fw@2soc#3vpn                    |
| 天融信数据库审计系统                  | superman      | telent                            |
| Hillstone安全审计平台                 | hillstone     | hillstone                         |
| 网康日志中心                          | ns25000       | ns25000                           |
| 网络安全审计系统（中科新业）          | admin         | 123456                            |
| 天玥网络安全审计系统                  | Admin         | cyberaudit                        |
| 明御WEB应用防火墙                     | admin         | admin                             |
|                                       | admin         | adminadmin                        |
| 明御攻防实验室平台                    | root          | 123456                            |
| 明御安全网关                          | admin         | adminadmin                        |
| 明御运维审计与册风险控制系统          | admin         | 1q2w3e                            |
|                                       | system        | 1q2w3e4r                          |
|                                       | auditor       | 1q2w3e4r                          |
|                                       | operator      | 1q2w3e4r                          |
| 明御网站卫士                          | sysmanager    | sysmanager888                     |
| 亿邮邮件网关                          | eyouuser      | eyou_admin                        |
|                                       | eyougw        | admin@(eyou)                      |
|                                       | admin         | +-ccccc                           |
|                                       | admin         | cyouadmin                         |
| Websense邮件安全网关                  | administrator | admin                             |
| 梭子鱼邮件存储网关                    | admin         | admin                             |