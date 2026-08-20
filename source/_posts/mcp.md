---
title: MCP 协议入门
date: 2026-08-19 21:02:51
tags: AI Agent
categories: "智能体应用"
top_img: https://bee-reg-ab.imagency.cn/p/1f2734017d86059d027c0b550a5d6ec4.jpeg
cover: https://bee-reg-ab.imagency.cn/p/1f2734017d86059d027c0b550a5d6ec4.jpeg
---

# MCP 介绍

模型上下文协议（Model Context Protocol，MCP）是一种标准化的开放协议，用于实现大语言模型（LLM）和外部数据源、工具、服务的集成，LLM 可凭借 MCP 协议调用外部资源和工具，类似人们访问互联网所需的超文本传输协议（HTTP）

学习更多 <https://www.runoob.com/vibe-coding/mcp-usage.html>

## MCP 架构

> （引用自Runoob）MCP 的架构由四个关键部分组成：
> 1. 主机（Host）：主机是期望从服务器获取数据的人工智能应用，例如一个集成开发环境（IDE）、聊天机器人等。主机负责初始化和管理客户端、处理用户授权、管理上下文聚合等。
> 2. 客户端（Client）：客户端是主机与服务器之间的桥梁。它与服务器保持一对一的连接，负责消息路由、能力管理、协议协商和订阅管理等。客户端确保主机和服务器之间的通信清晰、安全且高效。
> 3. 服务器（Server）：服务器是提供外部数据和工具的组件。它通过工具、资源和提示模板为大型语言模型提供额外的上下文和功能。例如，一个服务器可以提供与Gmail、Slack等外部服务的API调用。
> 4. 基础协议（Base Protocol）：基础协议定义了主机、客户端和服务器之间如何通信。它包括消息格式、生命周期管理和传输机制等。

如下图，MCP 协议可以比作电脑的 USB 转换器，通过它可以集成各种不同的服务

![](https://bee-reg-ab.imagency.cn/p/1f2734017d86059d027c0b550a5d6ec4.jpeg)

# MCP 服务器配置

为自己的 agent 配置 MCP 服务器，提供资源和工具，扩展 agent 的能力，

这里推荐一个**智谱视觉理解 MCP 服务器**

在前面文章我们介绍了搭建 Claude code + DeepSeek 开发环境，原文链接 [Claude Code + Deepseek-V4 + VScode开发环境搭建](/2026/07/09/claude-code/)，然而，DeepSeek 虽然具备强大的思考能力和文本理解能力，但是对图像、视频等多模态信息理解能力有限，图像理解仅停留在 OCR 文字识别能力上。

搭配图像理解 MCP，让 DeepSeek 能够调用理解图像和视频的工具，扩展了DeepSeek的能力

> 注：其他的一些 LLM （如：doubao、智谱 GLM 等）本身具备图像和视频理解能力，则无需配置该 MCP

## 开始之前

1. 智谱MCP需要 API key 才能访问使用。在[智谱BigModel平台](https://bigmodel.cn/apikey/platform) 上获取有效的 API key
2. 安装好 Claude code，并配置好 LLM，
3. 本地 Nodejs 版本 >18.0

## 快速配置

1. 一行命令快速配置

```bash
claude mcp add -s user zai-mcp-server --env Z_AI_API_KEY=YOUR_API_KEY -- npx -y "@z_ai/mcp-server"
```
其中 `YOUR_API_KEY` 替换为你的 api key。

> 命令解析： `add` 表示添加服务器，`-s` 表示作用域，`user` 表示用户级（全局安装，当前用户任何项目都可以使用），`zai-mcp-server`为服务器名称，未来可以根据该名称调用，`--envs` 表示向该 MCP 服务器运行环境注入环境变量，此处为你的 api key， `--` 为分隔符，表示参数传递结束，后文传递要执行的原始命令，`npx`命令则是临时下载并使用一个npm包，使用这个包实现与 MCP 服务器的联系，`-y` 表示跳过下载提示，直接同意。

使用以下命令查看自己配置的MCP，或者在对话中使用 `/mcp list`
```bash
claude mcp list
```

2. 手动配置

和上一步命令的表达含义一致，只是使用 json 的形式描述

打开 `~/.claude.json` 配置文件，进入到 `mcpServers`键，加入以下内容

```json
{
  "mcpServers": {
    "zai-mcp-server": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "@z_ai/mcp-server"
      ],
      "env": {
        "Z_AI_API_KEY": "YOUR_API_KEY",
        "Z_AI_MODE": "ZHIPU"
      }
    }
  }
}
```
同样的，记得替换 api key


## 使用方法

很简单，只要在对话中强调你的图像识别任务即可调用，例如，完整一点

```
这是一张短临预报模型项目架构图，使用zai-mcp-server图像理解 mcp，准确描述这张图片
```

模型会自动调用该 MCP 服务并返回相应结果

# 举一反三

智谱还有很多优质 MCP 服务，例如

1. 联网搜索 MCP：Claude code虽然自带 websearch 工具，但该 mcp 服务器的搜索能力更强，能够搜索更全面、更实时的信息
2. 网页读取 MCP：快速抓取网页内容并提供结构化数据，便于 agent 分析
3. 开源仓库 MCP：主要用于 GitHub 等开源仓库，让 agent 有条理地学习 GitHub 开源项目，加快项目理解。

根据自己需要，按照类似上述方式进行配置，具体可以参考官方技术文档 <https://docs.bigmodel.cn/cn/coding-plan/mcp/vision-mcp-server>

智谱之外，还有更大的 MCP 市场，根据自己的需求或 agent 目前的短板，筛选并应用更多 MCP 服务器来进一步提升 agent 的能力。

