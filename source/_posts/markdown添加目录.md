---
title: markdown添加目录
date: 2018-12-29 17:07:48
tags: 
categories: markdown
---

基于GitHub项目：https://github.com/ekalinin/github-markdown-toc

1. 安装执行文件

```
wget https://raw.githubusercontent.com/ekalinin/github-markdown-toc/master/gh-md-toc
// curl -O https://raw.githubusercontent.com/ekalinin/github-markdown-toc/master/gh-md-toc
chmod a+x gh-md-toc
```

2. 生成目录输出到命令行

```
./gh-md-toc ~/README.md

Table of Contents
=================

  * [Dockerfile.vim](#dockerfilevim)
  * [Screenshot](#screenshot)
  * [Installation](#installation)
        * [OR using Pathogen:](#or-using-pathogen)
        * [OR using Vundle:](#or-using-vundle)
  * [License](#license)
```