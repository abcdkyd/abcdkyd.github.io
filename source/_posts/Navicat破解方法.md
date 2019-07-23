---

title: Navicat破解方法
date: 2019-07-23 11:47:09
tags: Navicat
categories: 笔记

---

### Navicat破解方法

1. 下载官方试用版本
2. 生成自己的RSA公钥私钥对，注意密钥是2048位的，PKCS#8格式，在线工具 http://web.chacuo.net/netrsakeypair
3. 替换应用包内容目录中rpk文件的公钥
4. Mac版序列号密钥 
	- 中文版64位密钥序列号： NAVH-T4PX-WT8W-QBL5
	- 英文版64位密钥序列号： NAVG-UJ8Z-EVAP-JAUW
5. 解密激活请求码，生成激活码
	1. 打开应用，断网！！！，点击注册，输入密钥 NAVH-T4PX-WT8W-QBL5，然后手动激活
	2. 复制离线激活请求码，使用第一步提供的私钥解密请求码
	3. 加密激活码明文，使用第一步提供的私钥加密激活码明文

> - 在线RSA私钥解密 http://tool.chacuo.net/cryptrsaprikey
> - Mac版激活码明文格式如下：{"K":"NAVHT4PXWT8WQBL5", "N":"52pojie", "O":"52pojie.cn", "DI":"ODQ2Yjg2ZDBjMTEzMjhh", "T":1516939200}

`
{"K":"NAVHT4PXWT8WQBL5", "N":"vin", "O":"vin", "DI":"ZmYyZWU2NTFjMWFhOTlm", "T":1517236988}
`