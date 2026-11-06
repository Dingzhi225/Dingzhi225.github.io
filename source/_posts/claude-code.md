---
title: Claude Code + Deepseek-V4 + VScode开发环境搭建
date: 2026-07-09 14:39:29
tags: AI Agent
categories: "智能体应用"
top_img: https://bee-reg-ab.imagency.cn/e/bc6cbb00ee43d62f131077eff84d4576.jpg
cover: https://bee-reg-ab.imagency.cn/e/bc6cbb00ee43d62f131077eff84d4576.jpg
---

# 开始之前

1. 博主开发环境在“适用于Linux的Windows子系统”（WSL）和 MacOS上，WSL使用的 Linux 发行版为Ubuntu 22.04 LTS，对于WSL，Microsoft官方提供了详细的教程：<https://learn.microsoft.com/zh-cn/windows/wsl/>。

> MacOS 操作系统作为类 Unix 操作系统，操作方式与 Linux 很接近。本文主要侧重前两者环境下的 Claude code的配置和安装，Windows也会适当介绍，它与前两者流程基本一致但具体操作方法有些不同，详细内容可参考官方文档 <https://code.claude.com/docs/en/overview#native-install-recommended>

2. 你已经获取了Deepseek的官方应用程序接口（API），如果没有，请访问 <https://platform.deepseek.com/usage> ，按照官网要求获取API并充值。

3. 电脑上已经安装了VS code，如果没有，上官网可以快速安装。

# 快速开始
本次安装的是命令行界面（CLI）版本的 Claude code

## 方法一：使用安装脚本（官方推荐）
适用于不同环境，有不同的安装方法

适用于Linux，MacOS，WSL 的shell命令
```bash
curl -fsSL https://claude.ai/install.sh | bash
```
适用于 Windows Powershell 的命令
```powershell
irm https://claude.ai/install.ps1 | iex
```
适用于 MacOS Homebrew 的命令
```bash
brew install --cask claude-code
```
适用于 Windows winget 的命令
```powershell
winget install Anthropic.ClaudeCode
```
安装好后运行
```bash
claude --version
```
出现版本号之后表明安装成功

## 方法二：基于Node.js
### 安装Node.js

> Node.js® 是一个免费、开源、跨平台的 JavaScript 运行时环境，它让开发人员能够创建服务器、 Web 应用、命令行工具和脚本。

> 建议前往 Node.js [官方网站](https://nodejs.org/zh-cn)，按照官方文档描述进行安装，下面对 Ubuntu 进行一些补充

启动Ubuntu，打开bash命令行，Ubuntu搭载的软件包管理器（apt）提供的Node.js版本比较旧，Claude code要求Node.js版本不低于18，不建议用apt直接下载

> 注：不同操作系统使用不同的软件包管理器，例如，Windows 使用 winget，macOS 使用 Homebrew，CentOS 使用 yum，Fedora 使用 dnf 等。

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

安装完成后，运行
```bash
node --version && npm --version
```
出现版本号表示成功（npm 为Node package manager）

### 使用Node.js安装Claude code

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

> Windows 用户可以前往 git [官网](https://git-scm.com/about) 参考文档进行下载安装

# 配置Deepseek

有两种方法，不过殊途同归，个人更推荐第一种方法，避免污染全局环境

## 方法一：写入配置文件 settings.json（推荐）

第一次运行 Claude code，即使不成功，用户主目录（Linux：`/home/user_name/`，MacOS: `/Users/user_name/`，Windows: `C:\Users\user_name\`）中也会生成 `.claude` 目录，其中 `settings.json`是 Claude code 的配置文件

将上述配置以 JSON 文件的格式写入

```json
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "<你的 Deepseek API key>",
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek-v4-flash",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4-pro[1m]",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4-pro[1m]",
    "ANTHROPIC_MODEL": "deepseek-v4-pro[1m]",
    "CLAUDE_CODE_EFFORT_LEVEL": "max",
    "CLAUDE_CODE_SUBAGENT_MODEL": "deepseek-v4-flash"
  },
```
重启 Claude code 就会生效

该方法会更好，只作用于 Claude，不会影响整个终端。而且，该方法便于后期多 agent 和多 LLM 的管理，后续文章会提到。

## 方法二：写入环境变量
### MacOS或Linux

国内无法直接访问Claude code，所以先不急着打开（直接打开会显示网络问题然后退出）

对于 MacOS，Linux 系统，直接在终端中运行下列命令，或者打开 `~/.bashrc` 将下列命令写入环境变量
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

**到这里，你已经可以在CLI中运行你的智能体了**

### Windows

Windows 操作方法如下，首先检查 PowerShell 配置文件
```powershell
if (!(Test-Path -Path $PROFILE)) { New-Item -ItemType File -Path $PROFILE -Force }
```
之后使用记事本打开配置文件
```powershell
notepad $PROFILE
```
将以下内容写入到配置文件
```powershell
$env:ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
$env:ANTHROPIC_AUTH_TOKEN="<你的 DeepSeek API Key>"
$env:ANTHROPIC_MODEL="deepseek-flash[1m]"
$env:ANTHROPIC_DEFAULT_OPUS_MODEL="deepseek-flash[1m]"
$env:ANTHROPIC_DEFAULT_SONNET_MODEL="deepseek-flash[1m]"
$env:ANTHROPIC_DEFAULT_HAIKU_MODEL="deepseek-flash"
$env:CLAUDE_CODE_SUBAGENT_MODEL="deepseek-flash"
$env:CLAUDE_CODE_EFFORT_LEVEL="max"
```
后续在 PowerShell 上运行 Claude 同理

# VScode 配置
使用VScode连接到WSL（点击左下角图标，出现弹窗“连接到wsl”，选择）

在WSL上安装Claude code 扩展，安装好后打开扩展，此时侧边栏能够看到聊天框，说明配置已经自动完成了，**此时你可以在集成开发环境（IDE）上运行Claude code了**，当然命令行也可以运行，只是有部分内容会自动显示在VScode上（比如当AI介入文件修改时，文件的改动对比）。如果你更喜欢命令行界面，你也可以在扩展设置中选择 “Use Terminal”。

![](https://bee-reg-ab.imagency.cn/e/ffb1efcdc2e99311903c274bf12c8fd0.jpg)

如果侧边栏没有如期呈现聊天界面，而是提示需要登录，则需要在 Claude code 扩展设置中进一步配置

选择 environment variables 设置，进入 `setting.json` 配置文件，将上文[配置Deepseek](##配置Deepseek)设置的环境变量以 json 的格式写入到 `setting.json` 文件中，配置好后完整内容如下。

> 转换过程太麻烦？别忘了你已经可以请教 Claude code 了！打开命令行试试看！

```json
{
    "claudeCode.environmentVariables": [
        { "name": "ANTHROPIC_BASE_URL",
        "value": "https://api.deepseek.com/anthropic"},
        {"name": "ANTHROPIC_AUTH_TOKEN",
        "value": "你的 DeepSeek API key"},
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
}
```

