---
title: Ollama命令
date: 2026-8-6 21:14:27
tags: 
    - Ollama
categories: 笔记
---

## Ollama简介

Ollama 是一个开源的大型语言模型（LLM）平台，旨在让用户能够轻松地在本地运行、管理和与大型语言模型进行交互。

Ollama 提供了一个简单的方式来加载和使用各种预训练的语言模型，支持文本生成、翻译、代码编写、问答等多种自然语言处理任务。

Ollama 的特点在于它不仅仅提供了现成的模型和工具集，还提供了方便的界面和 API，使得从文本生成、对话系统到语义分析等任务都能快速实现。


## Ollama相关命令

可以输入命令 `ollama --help` 查看包含的命令
<!-- more -->
完整实例：
| 命令 (Command) | 说明 (Description) | 示例 (Example) |
| --- | --- | --- |
| **`ollama serve`** | **启动服务**。启动 Ollama 的 API 服务，端口默认是11434（通常后台自动运行）。 | `ollama serve` |
| **`ollama create`** | **创建模型**。根据 Modelfile 创建自定义模型（高级）。 | `ollama create my-bot -f ./Modelfile` |
| **`ollama show`** | **显示信息**。查看模型的元数据、参数或 Modelfile。 | `ollama show --modelfile llama3` |
| **`ollama run`** | **运行模型**。如果不存在则自动拉取。 | `ollama run llama3` |
| **`ollama stop`** | **停止模型**。停止运行中的模型。 | `ollama stop llama3` |
| **`ollama pull`** | **拉取模型**。从库中下载模型但不运行。 | `ollama pull mistral` |
| **`ollama push`** | **推送模型**。将你自定义的模型上传到 ollama。 | `ollama push my-username/my-model` |
| **`ollama list（或ls)`** | **列出模型**。显示本地所有已下载的模型。 | `ollama list（或ls)` |
| **`ollama ps`** | **查看进程**。显示当前正在运行的模型及显存占用。 | `ollama ps` |
| **`ollama cp`** | **复制模型**。将现有模型复制为新名称（用于测试）。 | `ollama cp llama3 my-model` |
| **`ollama rm`** | **删除模型**。移除本地模型释放空间。 | `ollama rm llama3` |
| **`ollama launch`** | **关联工具**。负责把各类编码工具（Claude Code、Codex、OpenCode、Moltbot 或任意其他 CLI 工具）与 Ollama 模型连接所需的一切设置。 | `ollama launch claude` |
| **`ollama help`** | **帮助**。查看任何命令的帮助信息。 | `ollama help run` |


## Ollama对话指令

在Ollama终端中提供了一系列指令，可以用来调整和控制对话模型，可以输入 `/?` 查看指令

完整实例：
| 命令 (Command) | 说明 (Description) | 示例 (Example) |
| --- | --- | --- |
| **`/bye`** | 退出当前控制台对话，快捷键: ctrl + d | `/bye` |
| **`/show`**| 查看当前模型详细信息 | `/show info|license|modelfile|parameters|system|template` |
| **`/set`** | 设置当前模型会话参数 | `/set parameter|system|template|history|nohistory|wordwrap|nowordwrap|format|noformat|verbose|quiet` |
| **`/load`** | 在对话过程中动态切换模型 | `/load deepseekp-coder` |
| **`/save`** | 把当前对话模型存储成一个新的模型 | `/save test` |
| **`/clear`** | 清除对话上下文内容 | `/clear` |
| **`/? shortcuts`** | 查询快捷键 | `/? shortcuts` |
| **`"""`** | 多行输入指令 | `"""你好<br/>你是什么模型？"""` |



