---
title: MAC安装Pygame
date: 2019-04-27 14:04:42
tags: 
	- Python
	- Pygame
	- MAC
categories: 环境配置
---

1. 安装pygame依赖包：`brew install hg sdl sdl_image sdl_ttf`，如果想启动更高级的功能，如在游戏中包含声音，可安装下面的两个额外的库：`brew install sdl_mixer portmidi`
2. 安装Pygame：`pip3 install --user hg+http://bitbucket.org/pygame/pygame`，如果报错可以尝试：`pip3 install pygame`
3. 验证 Pygame 是否安装成功，可以启动一个Python终端，输入：`import pygame`