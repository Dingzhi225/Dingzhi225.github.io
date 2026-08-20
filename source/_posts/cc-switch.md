---
title: 多 Agent和多 LLM 的管理——CC-Switch的使用
date: 2026-08-18 14:43:43
tags: 
- AI Agent
categories: "智能体应用"
top_img: https://bee-reg-ab.imagency.cn/p/5264a4ffa5ad2aa7e3d29a495ec4b047.png
cover: https://bee-reg-ab.imagency.cn/p/5264a4ffa5ad2aa7e3d29a495ec4b047.png
---

# 简介

上一篇文章 [Claude Code + Deepseek-V4 + VScode开发环境搭建](/2026/07/09/claude-code/) 已经介绍如何给自己的电脑搭配 Claude code 智能体（Agent），并配置 DeepSeek 大语言模型（LLM）。

然而，这些方法对于新手不友好的地方在于，需要自己手动操作命令、编辑配置文件实现配置，并且如果想要配置其他 LLM ，又需要手动修改文件，并不便捷

因此，本文推荐一款图形化操作的智能体管理工具——**CC-Switch**

以下是他的相关资源：

- Github: <https://github.com/farion1231/cc-switch>
- 中文官方网站/GUI下载: <https://www.ccswitch.io/zh/>
- Runoob 入门教程: <https://www.runoob.com/vibe-coding/cc-switch.html>

该应用兼容 Windows、 macOS和 Linux 多个操作系统

# 开始之前

1. 你已经拥有至少一个 AI agent（如 Claude code，codex）
2. 拥有至少一个 LLM （如 DeepSeek、doubao、ChatGPT 等）的 API key
3. 安装好 cc-switch

# 操作流程

Runoob 教程和官方网站已经介绍的很充分，概括起来就是以下步骤

1. 打开 cc-switch，找到你用的 AI Agent，目前支持的有 Claude code，codex，opencode，OpenClaw，Gemini，Grok 等
2. 点击右上角“+“号，添加供应商（即大语言模型）
3. 从已有的供应商列表中选择一个供应商，或者在下方创建自定义供应商
4. **（重要）在 API key 字段中写入你获取的 api key**
5. 设置模型映射，即不同的模型角色使用具体哪一个大语言模型，也就是下面一段配置所表达的含义，`[1m]` 表示1M 上下文能力的声明

```json
{
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek-v4-flash",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4-pro[1m]",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4-pro[1m]"
    }
```

6. 预览下方生成的配置JSON，确认无误后选择保存，即可写入配置文件并生效

# 常用功能

AI agent 支持的其他功能在 cc-switch 应用中也可以管理，例如右上角菜单栏可以发现

1. skill 统一管理：可以在其中管理适用于 agent 的 skill，应用 skill 可以强化 ai agent 特定方面的能力以及自动化水平，构建属于自己的自动化工作流
2. 提示词管理：定义常用提示词和项目提示词（如 CLAUDE.md），约束、控制agent的行为，提升其对用户意图的理解，减少幻觉出现的可能性
3. 对话管理：每一次对话agent 均会保存，使用该功能可以快速查看
4. MCP 服务器管理：管理各个模型上下文协议（Model Context Protocol，MCP）服务器，添加 MCP 服务器，为 agent 提供外部资源调用

## 本地路由

部分情况下，LLM 的 API 协议和 agent 的 API 协议并不相同，例如 codex 支持的 API 为 OpenAI Responses API，当配置其他 LLM 的时候，该 LLM 可能并不支持该 API 协议（例如 2026 年 8 月 1 日前，DeepSeek 对外提供 chat completions API 协议，并不支持 OpenAI Responses API 协议，因此 DeepSeek 无法直接接入 codex）

这时候，需要使用本地路由服务功能，实现 API 协议的接收-中转-发送

打开方法很简单，当你为 agent 添加 LLM 时，如果提示 “需要路由“，那么

1. 请打开左上角 “设置“，进入“路由“
2. 打开“路由总开关“和“在主页面显示本地路由开关“选择要启用路由的 agent，如 codex，这时本地路由服务会开始监听，端口一般为 `127.0.0.1:15721`
3. 回到主界面，启用该 LLM，点击连接测试，若连接正常，则可以使用

## 用量查询

tokens 的消耗的产生的费用可以到 LLM 提供商官网的账户主页查询，不过 cc-switch 支持本地监控费用和 tokens 的使用情况

由于不同 LLM 供应商的费用计算方式不同，有固定费用（套餐制）和 tokens 使用计费制，因此不同供应商的费用查询方式不同，部分需要手动配置

例如：

- DeepSeek：这个最方便，启动用量查询，选择“官方“，点击测试基本能成功，成功后保存
- Kimi：需要使用通用模板，配置特定的费用消耗提取器，配置代码如下

```js
({
  request: {
    url: "{{baseUrl}}/users/me/balance",
    method: "GET",
    headers: {
      "Authorization": "Bearer {{apiKey}}",
      "User-Agent": "cc-switch/1.0"
    }
  },
  extractor: function(response) {
    const balance = response?.available_balance ?? 0;
    return {
      isValid: true,
      total: balance,
      remaining: balance,
      unit: "CNY"
    };
  }
})
```

其他的配置方式还需要探索一下

