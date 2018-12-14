---
title: 添加php额外配置，引入php扩展
date: 2018-12-14 21:11:41
tags: php
categories: 环境配置
---

__`phpinfo`__ 中 `Scan this dir for additional .ini files` 配置参数是php扩展配置路径。

你可以在该路径下，配置文件结尾为`.ini`的文件作为你的额外配置参数。

我们可以利用它来更好的管理我们php的扩展的相关配置：

引用pdo_pgsql扩展
`extension="/usr/local/opt/php71-pdo-pgsql/pdo_pgsql.so"`