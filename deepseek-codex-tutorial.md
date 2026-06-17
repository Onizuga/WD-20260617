# 中国国内网络环境下使用 DeepSeek + Codex 完整教程

## 概述

本教程将指导你**从零开始**，在中国国内网络环境下完成：

1. 安装 Codex（AI 编程助手）
2. 配置 DeepSeek 大模型（国产 AI 模型，无需翻墙）
3. 在内网/离线环境下使用 Codex
4. 常见问题排查

**适用人群：** 编程小白、开发者、需要在受限网络下使用 AI 编程的同学

---

## 一、了解你需要的东西

### 什么是 Codex？

**Codex** 是一个命令行 AI 编程助手。它能：
- 理解你的自然语言指令并写代码
- 读取项目代码并给出修改建议
- 帮你调试、重构、生成测试

### 什么是 DeepSeek？

**DeepSeek**（深度求索）是国产大语言模型，在中国国内可以直接访问：
- 官网：https://platform.deepseek.com
- API 原生支持中文
- 国内网络畅通无阻，无需任何翻墙工具

### 你需要准备的

| 项目 | 说明 | 费用 |
|------|------|------|
| 一台电脑 | Windows / macOS / Linux 均可 | -- |
| Node.js | Codex 依赖的运行环境 | 免费 |
| Python 3 | 部分场景可能需要 | 免费 |
| DeepSeek API Key | 调用 AI 模型的凭证 | 按量付费（注册送 500 万 tokens） |
| Git | 代码版本管理（可选） | 免费 |

---

## 二、基础环境安装

### 2.1 安装 Node.js

Codex 依赖 Node.js 运行。

**Windows 用户：**
1. 打开浏览器，访问 https://nodejs.org/
2. 下载 **LTS 版本**（左侧按钮，如 22.x）
3. 双击下载的 \`.msi\` 安装包，一路点击「Next」
4. 安装完成后，打开**命令提示符（cmd）**或 **PowerShell**
5. 输入以下命令验证安装：
   `node --version`
   `npm --version`
   如果显示版本号（如 v22.16.0），说明安装成功

**国内镜像加速（推荐）：**
如果 npm 下载包太慢，配置国内镜像源：
`npm config set registry https://registry.npmmirror.com`

### 2.2 安装 Git（可选但有帮助）

1. 访问 https://git-scm.com/download/win
2. 下载安装包，一路默认安装
3. 验证安装：打开终端输入 `git --version`

### 2.3 安装 curl（用于调用 API）

**Windows 10/11 用户：** 系统已自带 curl.exe，直接在终端输入 `curl --version` 确认。
**macOS/Linux 用户：** 系统也已预装。

---

## 三、注册 DeepSeek 并获取 API Key

### 3.1 注册 DeepSeek 账号

1. 打开浏览器访问 **https://platform.deepseek.com**
2. 点击右上角 **「Sign Up」**（注册）
3. 可用邮箱（QQ 邮箱、163 邮箱等均可）或手机号注册
4. 注册成功后登录

### 3.2 获取 API Key

1. 登录后，在左侧菜单找到 **「API Keys」**
2. 点击 **「Create API Key」**
3. 给你的 Key 起个名字（如 \`my-codex-key\`）
4. 点击创建，**立即复制并保存好这个 Key**（页面关闭后就看不到了）
5. Key 的格式类似：\`sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\`

> 注意：DeepSeek 新用户注册会赠送 500 万 tokens（约 500 万汉字），足够试用很久。

### 3.3 查看 DeepSeek 模型名称

DeepSeek 提供的 API 模型：
- **deepseek-chat** — 通用对话模型（推荐用于 Codex）
- **deepseek-reasoner** — 推理模型（适合复杂逻辑）
- **deepseek-coder** — 代码专用模型（针对编程优化）

Codex 建议使用 **deepseek-chat** 或 **deepseek-coder**。

---

## 四、安装 Codex

### 4.1 全局安装 Codex

打开终端（PowerShell / cmd / Terminal），运行：

`npm install -g @openai/codex`

> 如果已使用国内镜像源（执行了 2.1 节），下载会很快。

等待安装完成，验证安装：
`codex --version`
如果显示版本号，说明安装成功。

### 4.2 替代方案：使用 npx 运行

如果你不想全局安装，也可以用 npx 直接运行：
`npx @openai/codex`
但本教程以全局安装为例。

---

## 五、配置 Codex 使用 DeepSeek

### 5.1 设置环境变量

Codex 通过环境变量读取 API Key 和模型配置。

**Windows PowerShell 用户：**

打开 PowerShell，执行以下命令。**注意：** 把 \`sk-xxx\` 替换为你的真实 API Key。

```
# 设置 DeepSeek API Key
[System.Environment]::SetEnvironmentVariable('CODEX_API_KEY', 'sk-你的deepseek-api-key', 'User')

# 设置 DeepSeek 的 API 地址
[System.Environment]::SetEnvironmentVariable('CODEX_BASE_URL', 'https://api.deepseek.com', 'User')

# 设置模型名称（推荐 deepseek-coder 或 deepseek-chat）
[System.Environment]::SetEnvironmentVariable('CODEX_MODEL', 'deepseek-chat', 'User')
```

> 解释：\`'User'\` 表示将此环境变量写入用户级环境变量中（重启后依然有效）。

**macOS / Linux 用户：**

编辑 \`~/.bashrc\` 或 \`~/.zshrc\`，加入：
```
export CODEX_API_KEY='sk-你的deepseek-api-key'
export CODEX_BASE_URL='https://api.deepseek.com'
export CODEX_MODEL='deepseek-chat'
```
然后执行 \`source ~/.bashrc\`（或重启终端）。

### 5.2 验证配置

在新开的终端窗口中执行：
```
# Windows
echo $env:CODEX_API_KEY
echo $env:CODEX_BASE_URL
echo $env:CODEX_MODEL

# macOS/Linux
echo $CODEX_API_KEY
echo $CODEX_BASE_URL
echo $CODEX_MODEL
```

如果能显示你刚才设置的值，说明配置生效。

### 5.3 测试 DeepSeek 连接

```
curl https://api.deepseek.com/v1/models -H "Authorization: Bearer $env:CODEX_API_KEY"
```

如果返回包含模型列表的 JSON，说明 DeepSeek API 连接正常。

---

## 六、使用 Codex 进行编程

### 6.1 交互模式

在终端中直接输入：
`codex`

然后进入交互模式，你可以输入自然语言指令，例如：
> 创建一个 Python 脚本，读取当前目录下所有 CSV 文件并合并

Codex 会分析你的请求，生成代码，并说明做了什么。

### 6.2 直接命令行提问

你也可以直接附带问题：
`codex "用 Python 写一个快速排序算法"`

### 6.3 在项目中工作

1. 进入你的项目目录：
   `cd 你的项目路径`

2. 启动 Codex：
   `codex`

3. 现在 Codex 会读取你项目的文件结构，你可以问：
   > 帮我检查这个项目的代码有没有 bug
   > 给这个函数写单元测试
   > 创建一个新的 REST API 端点

---

## 七、内网/离线环境使用方案

### 7.1 完全离线（无互联网）

如果内网完全无法访问互联网，有以下选择：

**方案 A：使用本地模型（推荐 Ollama）**

1. 在外网电脑安装 Ollama：
   - macOS / Linux：\`curl -fsSL https://ollama.com/install.sh | sh\`
   - Windows：从 https://ollama.com/download 下载安装包

2. 下载 DeepSeek 的本地模型（需要外网下载一次）：
   \`ollama pull deepseek-coder:6.7b\`
   这个命令会下载约 4GB 的模型文件。

3. 下载完成后，把模型拷贝到内网机器（U盘/内部NAS）：
   - Windows 模型位置：\`C:\\Users\\你的用户名\\.ollama\\models\\\`
   - macOS 模型位置：\`~/.ollama/models/\`
   - 复制整个 \`blobs\` 文件夹

4. 在内网安装 Ollama 并把模型文件放到同样位置

5. 启动 Ollama 服务：
   \`ollama serve\`

6. 配置 Codex 连接本地模型：
```
[System.Environment]::SetEnvironmentVariable('CODEX_BASE_URL', 'http://localhost:11434/v1', 'User')
[System.Environment]::SetEnvironmentVariable('CODEX_API_KEY', 'ollama', 'User')
[System.Environment]::SetEnvironmentVariable('CODEX_MODEL', 'deepseek-coder:6.7b', 'User')
```

**方案 B：使用 HTTP 代理**

如果内网可以通过 HTTP 代理访问外网：
```
[System.Environment]::SetEnvironmentVariable('HTTP_PROXY', 'http://你的代理地址:端口', 'User')
[System.Environment]::SetEnvironmentVariable('HTTPS_PROXY', 'http://你的代理地址:端口', 'User')
```

### 7.2 半离线（部分网站能访问）

如果只是 GitHub 被限制，但 DeepSeek API 可以访问（DeepSeek 在国内无需翻墙），那么你只需要配置 DeepSeek 即可（按第五部分），完全不需要代理。

---

## 八、常见问题排查

### Q1：Codex 提示 "API connection failed"

**原因：** 无法连接 DeepSeek API。
**解决：**
1. 检查 API Key 是否正确
2. 检查网络是否能访问 \`api.deepseek.com\`
3. 用 curl 测试：\`curl -I https://api.deepseek.com\`
4. 如果在内网，参考第七部分配置代理

### Q2：Codex 提示 "Model not found"

**原因：** 模型名称写错了。
**解决：**
1. 确保 \`CODEX_MODEL\` 设置为正确的值：\`deepseek-chat\`、\`deepseek-coder\` 或 \`deepseek-reasoner\`
2. 如果不确定，先设为 \`deepseek-chat\`

### Q3：npm install 非常慢或失败

**原因：** 默认 npm 源在国外。
**解决：**
\`npm config set registry https://registry.npmmirror.com\`

### Q4：环境变量设置了但 Codex 不生效

**原因：** 环境变量修改后需要新开终端窗口。
**解决：**
- 关掉当前终端，重新打开一个新的

### Q5：Codex 中文显示乱码

**原因：** 终端编码问题。
**解决：**
在 PowerShell 中先执行：
\`chcp 65001\`

---

## 九、快速参考卡片

### Windows 一键配置脚本

将下面内容保存为 \`setup-codex.ps1\`，把 \`YOUR_DEEPSEEK_KEY\` 替换为你的真实 Key 后运行：

```
# === Codex + DeepSeek 一键配置脚本 (Windows) ===
npm config set registry https://registry.npmmirror.com
npm install -g @openai/codex
[System.Environment]::SetEnvironmentVariable('CODEX_API_KEY', 'YOUR_DEEPSEEK_KEY', 'User')
[System.Environment]::SetEnvironmentVariable('CODEX_BASE_URL', 'https://api.deepseek.com', 'User')
[System.Environment]::SetEnvironmentVariable('CODEX_MODEL', 'deepseek-chat', 'User')
Write-Host '配置完成！请重新打开终端后使用 codex 命令'
```

### macOS / Linux 一键配置脚本

```
#!/bin/bash
npm config set registry https://registry.npmmirror.com
npm install -g @openai/codex
echo 'export CODEX_API_KEY="YOUR_DEEPSEEK_KEY"' >> ~/.bashrc
echo 'export CODEX_BASE_URL="https://api.deepseek.com"' >> ~/.bashrc
echo 'export CODEX_MODEL="deepseek-chat"' >> ~/.bashrc
source ~/.bashrc
echo '配置完成！请重新打开终端后使用 codex 命令'
```

---

## 相关链接

| 资源 | 地址 |
|------|------|
| DeepSeek 官网 | https://platform.deepseek.com |
| DeepSeek API 文档 | https://api-docs.deepseek.com |
| Node.js 下载 | https://nodejs.org |
| npm 国内镜像 | https://npmmirror.com |
| Git 下载 | https://git-scm.com |
| Ollama 官网 | https://ollama.com |
