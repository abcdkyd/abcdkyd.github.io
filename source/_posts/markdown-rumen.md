---
title: Markdown 简单语法
date: 2017-03-28 14:17:06
tags: markdown
categories: 效率
---

Markdown是一种轻量级的标记语言，可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。

<!-- more -->
# 简要规则

### 标题
1.在需要作为标题的内容前添加 `#`
一共6级标题，右边可补充等量或不等量闭合符号，标题等级由前面 `#` 数量决定
如：
```
# 一级标题
## 二级标题
### 三级标题
### 三级标题 ####
```

2.在标题下添加两个以上的 `=` （最高阶标题） 和 `-` （第二阶标题）
如：
```
一级标题
====
二级标题
----
```
        
### 引用
在需要引用的内容前添加 `>`
如：
```
> 这是引用的内容
```

### 列表
无序列表使用 `*`、`-` 和 `+`，有序列表使用数字加英文句点 `1.`
如：
```
* 1
* 2
* 3
 
- 1
- 2
- 3

1. 1
2. 2
3. 3
```

### 粗体和斜体
在需要设置的文字两端添加 `*` 或 `_`
斜体1个 `*斜体*、_斜体_`，粗体2个 `**粗体**、__粗体__`
如：
```
***斜体加粗***
~~删除线~~
```
***斜体加粗***
~~删除线~~

### 分隔线
在一行中用三个以上的 `*`、 `-`、 `_` 来建立一个分隔线，行内不能有其他东西。也可以在符号号中间插入空格。
如：
```
***
* * *
- - -
______
```

### 表格
```
|title|name|
|---:|:--:|
|title1|name1|
```
|title|name|
|---:|:--:|
|title1|name1|

### 链接
链接有两种方式，内联方式和引用方式
内联方式：
`[]` 里面是链接的文字，`()` 里面是链接的地址
```
这是[百度](http://www.baidu.com)的链接
```
这是[百度](http://www.baidu.com)的链接

引用方式：
`[]` 里面是引用标记，接着是链接的地址，最后可以加上链接的title属性
```
[百度一下][baidu]、[必应][biying]
[baidu]: http://www.baidu.com "鼠标停留显示的文字"
[biying]: http://cn.bing.com "鼠标停留显示的文字"
```
[百度一下][baidu]、[必应][biying]
[baidu]: http://www.baidu.com "鼠标停留显示的文字"
[biying]: http://cn.bing.com "鼠标停留显示的文字"

### 图片
图片跟链接格式相似，同样有内联方式和引用方式，区别是图片前面多一个 `!`
内联方式：
```
![Alt text](/path/to/img.jpg "Optional title")
```

引用方式：
```
![Alt text][id]
[id]: url/to/image  "Optional title"
```

### Emoji 表情

`:blush:` :blush: `:smile:` :smile: `:joy:` :joy: `:sob:` :sob:

[表情名称参考](https://github.com/abcdkyd/abcdkyd.github.io/blob/hexo/emoji.md)

**让hexo支持emoji表情**

  markdown 变成html的转换器叫做`markdown渲染器`，在Hexo中默认的markdown渲染器 使用的是`hexo-renderer-marked`,是Hexo版本，这个渲染器不支持插件扩展。
  另外一个 markdown 渲染器 `hexo-renderer-markdown-it`，这个支持插件配置，可以使用 markwon-it-emoji插件来支持emoji。需要将原来的 marked 渲染器换成 markdown-it渲染器。

```
# cd Documents/blog
# npm un hexo-renderer-marked --save
# npm i hexo-renderer-markdown-it --save
```

安装markdown-it-emoji插件
```
npm install markdown-it-emoji --save
```

配置根目录下的_config.yml，添加以下配置
```
# Markdown-it config
## Docs: https://github.com/celsomiranda/hexo-renderer-markdown-it/wiki
markdown:
  render:
    html: true
    xhtmlOut: false
    breaks: true
    linkify: true
    typographer: true
    quotes: '“”‘’'
  plugins:
    - markdown-it-abbr
    - markdown-it-footnote
    - markdown-it-ins
    - markdown-it-sub
    - markdown-it-sup
    - markdown-it-emoji  # add emoji
  anchors:
    level: 2
    collisionSuffix: 'v'
    permalink: true
    permalinkClass: header-anchor
    permalinkSymbol: ¶
```

### 代码块
用 `` ` `` 标记简单的代码行
用 ` ```  ` 或 4个空格， 标记代码段，` ``` `后面可以跟代码类型来实现高亮 如：` ``` php `。
