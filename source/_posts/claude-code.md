---
title: Claude Code + Deepseek-V4 + VScode开发环境搭建
date: 2026-07-09 14:39:29
tags: AI Agent
categories: "智能体应用"
top_img: https://bee-reg-ab.imagency.cn/e/bc6cbb00ee43d62f131077eff84d4576.jpg
cover: https://bee-reg-ab.imagency.cn/e/bc6cbb00ee43d62f131077eff84d4576.jpg
---

# 开始之前

1. 本文开发环境建立在“适用于Linux的Windows子系统”（WSL）上，使用的发行版为Ubuntu 22.04 LTS，个人喜欢在Linux操作系统上进行编程和开发，一旦上手了就会感觉很方便。对于WSL，Microsoft官方提供了详细的教程：<https://learn.microsoft.com/zh-cn/windows/wsl/>。

> macOS 操作系统作为类 Unix 操作系统，操作方式与 Linux 相似，因此本文操作方法在 macOS 也可以参考。

2. 你已经获取了Deepseek的官方应用程序接口（API），如果没有，请访问 <https://platform.deepseek.com/usage> ，按照官网要求获取API并充值。

3. 电脑上已经安装了VS code（安装在Windows上，无需安装在WSL上，只需要在VScode上安装remote ssh和wsl扩展即可），如果没有，上官网可以快速安装。

# 快速开始
## 安装Node.js

> Node.js® 是一个免费、开源、跨平台的 JavaScript 运行时环境，它让开发人员能够创建服务器、 Web 应用、命令行工具和脚本。

启动Ubuntu，打开bash命令行

Ubuntu搭载的软件包管理器（apt）提供的Node.js版本比较旧，Claude code要求Node.js版本不低于18，不建议用apt直接下载

使用curl命令下载一段用于设置Node.js安装源的bash脚本，并调用bash直接运行

```bash
curl -sL https://deb.nodesource.com/setup_20.x | sudo bash - 
```
URL当中的 `setup_20.x` 表示搜索版本在20的Node.js，你也可以安装更高版本，例如24。

> 命令解析：`curl` 是Linux一个利用URL规则在命令行工作的文件传输工具，`-s` 表示使用静默模式，不输出进度信息；`-L` 表示跟随网站重定向（301、302），`|` 是管道符，用于连接前后两个命令（前者命令执行成功后就执行后续命令），`sudo bash -` 表示借助管理员权限使用bash运行下载的脚本，如果你想先保存并阅读脚本，你可以 `curl -o setup.sh https://deb.nodesource.com/setup_20.x` 即下载脚本到文件`setup.sh`，之后再决定执行。

上述命令运行成功后，可以运行查看可用的Node.js
```bash
sudo apt list -a nodejs
```
也可以直接安装最新版
```bash 
sudo apt install nodejs
```
> macOS 使用 Homebrew 进行安装

安装完成后，运行
```bash
node --version && npm --version
```
出现版本号表示成功（npm 为Node package manager）

## 安装Git
> Git 是一个免费的开源版本控制系统，适用于软件开发。得益于Github、Gitlab等Git托管服务，Git在今天被广泛使用，并且与编辑器深度集成。

使用Claude code会进行很多代码改动，使用git可以轻松查看改动记录，进行版本控制。

运行命令直接安装
```bash
sudo apt install git
```
安装完成后，运行
```bash
git --version
```
出现版本号则运行成功。

## 使用Node.js安装Claude code

运行以下命令
```bash
npm install -g @anthropic-ai/claude-code
```
> 命令解析：`-g` 表示全局安装（所有项目均可访问），没有该选项表示安装到当前项目目录（自动创建 `node_modules` 目录），可以运行 `npm list -g --depth=0` 来查看当前安装的模块

安装好后运行
```bash
claude --version
```
出现版本号之后表明安装成功

## 配置Deepseek
国内无法直接访问Claude code，所以先不急着打开（直接打开会显示网络问题然后退出）

直接在终端中运行下列命令，或者打开 `~/.bashrc` 将下列命令写入环境变量
```bash
# Claude code
export ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
export ANTHROPIC_AUTH_TOKEN="<你的 DeepSeek API Key>"
export ANTHROPIC_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_OPUS_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_SONNET_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_HAIKU_MODEL=deepseek-v4-flash
export CLAUDE_CODE_SUBAGENT_MODEL=deepseek-v4-flash
export CLAUDE_CODE_EFFORT_LEVEL=max
```
建议写入环境变量，否则终端关闭后会失效，写入后重新运行
```bash
source ~/.bashrc
```
> 命令解析：`source` 表示运行指定文件，与 `./` 直接运行不同，该命令运行后所有新增的shell变量会保留在当前环境，不会创建新shell环境，不会创建新进程

此时再运行
```bash
claude
```
如果没有拒绝访问，出现配置页面，说明上述步骤已经完成，具体Claude配置按照新手引导信息自主选择即可。

注意，你在某一目录下运行Claude，该目录默认被视为他的工作目录，因此，建议不要在根目录上运行。

**到这里，你已经可以在命令行界面（CLI）中运行你的智能体了**

## VScode 配置
使用VScode连接到WSL（点击左下角图标，出现弹窗“连接到wsl”，选择）

在WSL上安装Claude code 扩展，安装好后打开扩展，此时侧边栏能够看到聊天框，说明配置已经自动完成了，**此时你可以在集成开发环境（IDE）上运行Claude code了**，当然命令行也可以运行，只是有部分内容会自动显示在VScode上（比如当AI介入文件修改时，文件的改动对比）。如果你更喜欢命令行界面，你也可以在扩展设置中选择 “Use Terminal”。

![](https://bee-reg-ab.imagency.cn/e/ffb1efcdc2e99311903c274bf12c8fd0.jpg)

如果侧边栏没有如期呈现聊天界面，而是提示需要登录，则需要在 Claude code 扩展设置中进一步配置

选择 environment variables 设置，进入 `setting.json` 配置文件，将上文[配置Deepseek](##配置Deepseek)设置的环境变量以 json 的格式写入到 `setting.json` 文件中，配置好后完整内容如下。

> 转换成json过程太麻烦？别忘了你已经可以请教 Claude code 了！打开命令行试试看！

```json
{
    "editor.fontSize": 14,
    "files.autoSave": "afterDelay",
    "terminal.integrated.mouseWheelScrollSensitivity": 3,
    "terminal.integrated.gpuAcceleration": "off",
    "claudeCode.environmentVariables": [
    
        { "name": "ANTHROPIC_BASE_URL",
        "value": "https://api.deepseek.com/anthropic"},
        {"name": "ANTHROPIC_AUTH_TOKEN",
        "value": "sk-facffc5d13ad40d58e51296c6d13ce4c"},
        {"name": "ANTHROPIC_MODEL",
        "value": "deepseek-v4-pro[1m]"},
        {"name": "ANTHROPIC_DEFAULT_OPUS_MODEL",
        "value": "deepseek-v4-pro[1m]"},
        {"name": "ANTHROPIC_DEFAULT_SONNET_MODEL",
        "value": "deepseek-v4-pro[1m]"},
        {"name": "ANTHROPIC_DEFAULT_HAIKU_MODEL",
        "value": "deepseek-v4-flash"},
        {"name": "CLAUDE_CODE_SUBAGENT_MODEL",
        "value": "deepseek-v4-flash"},
        {"name": "CLAUDE_CODE_EFFORT_LEVEL",
        "value": "max"}

    ],
    "terminal.integrated.fontSize": 13
}
```

