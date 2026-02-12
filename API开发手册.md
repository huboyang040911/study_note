# API开发手册

## 一、API的概念

### 1.1 API 的基础

API 的全称是 Application Programming Interface（应用程序编程接口）。可以类比为电源插座。

当你把手机充电器插进插座时，你并不需要知道远处的发电厂是在烧煤还是在用风力发电，也不需要理解复杂的电网是怎么把电输送到你家的。对你来说，电厂就是一个“黑盒子”。你唯一关心的就是这个插座：只要你的插头尺寸对得上（符合标准），一插进去，电就来了。

开发者不需要从头去研究如何发电（比如画地图、写支付系统），只需要调用现成的 API，就能像**搭积木一样**快速构建出功能强大的应用。这不仅节省了巨大的开发成本，还让**自动化**成了可能——比如让系统自动处理重复性工作，同步数据或发送通知，把人类从琐碎的劳动中解放出来。同时，API 还充当了**安全守门人**的角色，确保只有合法的插头（请求）才能接通，让企业可以放心地使用第三方提供的尖端服务。

### 1.2 API如何工作

如果把API的运行过程比作再餐厅点餐：

在这个过程中，**API 请求**就像是你的订单。它包含了你想访问的“端点”（菜名）、你想做的动作（是获取数据还是提交信息）以及具体的参数（比如只要伦敦的天气）。而 **API 响应** 则是服务员带回的结果，其中包含了告诉你是否成功的“状态码”（比如 200 代表大功告成）以及你真正想要的“响应体”数据。

### 1.3 示例

**天气预报 API** 它就像一个专门提供气象信息的窗口。你的天气应用向它发送坐标和密钥，它就回传一串 JSON 数据。

- 一个简单的“请求”示例：
  - **Endpoint:** `/current-weather`
  - **Parameters:** `city=London` & `apiKey=your_access_key`
- **“响应”（回传的数据）：**`json { "city": "London", "temperature": "15°C", "condition": "Cloudy", "humidity": "70%" } `应用拿到这些数据后，再把它们漂亮地展示在你的手机屏幕上。

### 1.5 安全提示

在设计前端网页时，不要再浏览器代码里直接写API密钥，通常的做法是写一个后端代理，让服务器完成调用。

## 二、Git工作流

### 2.1 什么是Git

Git 是由 Linux 内核开发者 Linus Torvalds 于 2005 年创建的分布式版本控制系统。其核心功能是跟踪文件的修改历史，允许开发者随时查看和回滚到以前的版本，并在与他人协作时高效地合并更改。

### 2.2 安装Git

Windows系统可以前往官网安装后下载，根据安装向导提示安装即可。

Linux系统：

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install git

# CentOS/RHEL
sudo yum install git
```

### 2.3 初始化Git

安装 Git 后，你首先需要配置你的用户信息——这是使用 Git 进行版本控制的基本步骤。

```bash
# 设置全局用户名（将显示在提交记录中）
git config --global user.name "Your Name"

# 设置全局邮箱（建议使用在 GitHub/GitLab 等平台上注册的邮箱）
git config --global user.email "your.email@example.com"
```

Git 会将此信息嵌入到每个提交记录中，作为每次修改的“作者信息”。查看版本历史记录（例如，使用 git log）时，你可以清楚地看到谁修改了每一行代码，便于追溯责任和沟通。

查看当前Git的配置信息：

```bash
git config --list
```

### 2.4 GitHub介绍

GitHub 是一个基于 Git 的代码托管平台。它不仅为 Git 仓库提供远程存储，还包括协作工具（如 Issues、Pull Requests、Projects），使开发者更容易分享代码和协作。

拉取远程仓库：

一般分为https以及SSH的方式。一般来说，通过 HTTP 克隆的仓库适合临时下载和测试他人的仓库，但不建议用于自己的开发。为了更好的学习体验，你应该先设置 SSH 认证。

- **HTTPS 方式**：基于通用的 HTTPS 协议（443 端口），访问 GitHub 仓库时需要用「用户名 + 个人访问令牌（PAT）」完成身份验证（GitHub 已不再支持密码直接认证）。

- **SSH 方式**：基于 SSH 安全外壳协议（22 端口），通过本地生成的「SSH 密钥对」（公钥 + 私钥）认证：把公钥添加到 GitHub 账户，本地保留私钥，GitHub 通过公钥验证你的私钥合法性，无需输入密码 / 令牌。

SSH 认证依赖于密钥对（公钥 + 私钥），它们是匹配的加密文件。生成后，你需要将“公钥”提供给 GitHub（“绑定”），而“私钥”留在本地设备上：

1. 私钥：存储在本地设备（如电脑）的指定目录中（通常是 ~/.ssh/），充当“你的专属钥匙”，绝不能与任何人分享。
2. 公钥：这是一把可以公开分享的“锁”——你需要将其复制到 GitHub 账号的“SSH keys list”中（“绑定”操作）。

也就是说想使用SSH认证，则需要**生成密钥对-上传公钥到GitHub**。私钥保存到本地，公钥上传到GitHub中。生成密钥对的命令：

```bash
# 执行命令后的交互过程示例
$ ssh-keygen -t ed25519 -C "your_email@example.com"
Generating public/private ed25519 key pair.
# 提示1：指定密钥保存路径（回车=使用默认路径~/.ssh/id_ed25519）
Enter file in which to save the key (/root/.ssh/id_ed25519): 
# 提示2：设置私钥的密码保护（passphrase，回车=无密码）
Enter passphrase (empty for no passphrase): 
# 提示3：确认密码保护（回车=无密码）
Enter same passphrase again: 
```

如果你有多个设备（如笔记本电脑和台式机），你需要为每个设备生成单独的 SSH 密钥对，并将每个公钥绑定到同一个 GitHub 账号——每个设备都有自己的“门禁卡”。

### 2.5 Git的基本操作

1.下载已有仓库

```bash
git clone
```

2.从远程仓库获取更新

每次更新仓库之前，由于它可能由多人维护，你需要先拉取最新的更改。之后，你可以修改并推送文件。注意包含仓库的相对路径。

```bash
git pull
```

3.暂存更新并推送至GitHub

```bash
git status # 查看当前文件修改状态
git add xxx.xx # 添加单个文件
git add . # 添加所有修改的文件
git commit -m "..." # 提交暂存区的修改，并添加提交信息
git pull origin main # 先拉取远程最新代码
git push origin main # 明确指定远程仓库和分支(首次需要加上-u)
```

## 三、Web应用部署

为什么需要部署？

任何一个网站想要被外部用户访问，都必须有一个可以**公开访问的网络地址**（这个地址可以是 IP 地址，比如 123.45.67.89，也可以是域名，比如 [google.com](https://google.com/) 等）。但只有地址是不够的——你写好的网页代码（例如 HTML、CSS、JavaScript 文件，或者使用 React、Vue 等框架写的项目），以及相关的图片 / 视频资源，都必须"放"在一台 24 小时在线的服务器上，由它来响应网络请求，这样任何人的浏览器才能访问并下载这些资源。

![image1.kHLtXFq8](D:\study\AI-basic\images\image1.kHLtXFq8.png)

因此，把资源上传、配置好环境并让服务"跑起来"的整个过程，就被称为 **部署（Deployment）**。

### 3.1 部署的常见步骤

1.**服务器准备**：你需要先购买云服务器（比如阿里云、腾讯云、或 AWS EC2），选择服务器所在地区（如上海、新加坡）、配置（CPU、内存、磁盘大小等），还要学会如何远程连接服务器（例如通过 SSH 工具登录）。

2.**环境配置**：Web 应用需要在特定"环境"中才能运行——例如运行 Node.js 项目必须先安装 Node.js；运行 Python 项目必须安装 Python 以及对应的第三方库。

3.**上传资源**：你需要把本地的代码和资源上传到服务器上，常用的方法包括 FTP 或 Git。如果项目体积比较大（比如包含视频文件），中途一旦断线，有时需要重新上传。

4.**启动服务并测试**：上传完成后，你还需要在服务器上执行命令启动应用，并测试"分配的网络地址是否能访问"。

5.**维护与更新**：后续每次你修改代码，都要重新上传并重启服务。如果服务器宕机（例如断电、网络故障），还需要手动重启应用，有时还要额外配置"进程守护工具"，让程序在异常退出后自动拉起。

上述步骤通常比较繁琐。建议使用低代码部署平台，如：CloudBase（腾讯云）、Vercel、Zeabur。它们会帮你自动完成"买服务器、配环境、上传代码、启动服务、监控运行"等步骤。你只需要把自己的代码仓库（比如 GitHub 或 GitLab）连接到平台，它就会自动拉取代码、识别应用类型、配置对应的运行时环境，最后给你一个可以被任何人访问的公网地址。它甚至可以一键绑定你自己的域名。

| 平台                 | 特点                               | 适用场景                                                     | 免费额度              |
| -------------------- | ---------------------------------- | ------------------------------------------------------------ | --------------------- |
| **腾讯云 CloudBase** | 国内访问速度快，与微信生态深度整合 | 1.国内用户友好<br />2.微信生态整合好<br />3.充足的免费额度   | 有免费额度            |
| **Vercel**           | 前端框架支持好，与 GitHub 集成紧密 | 1.最流行的前端部署平台<br />2.GitHub深度集成                 | 有免费额度            |
| **Zeabur**           | 支持多种语言和服务模板，配置灵活   | 1.内置 Dify、n8n、数据库等多种服务模板<br />2.GitHub、模板、Docker 镜像、本地项目等 | 每月约 5 美元免费额度 |

### 3.2 CloudBase

- 注册并创建环境，登录腾讯云控制台，创建一个新环境。

- 开通静态网站托管，进入网站静态托管界面，这里已经分配了一个默认的域名。

  ![image-20260209113909909](D:\study\AI-basic\images\image-20260209113909909.png)

  支持多种部署模板：

  ![image-20260209114007768](D:\study\AI-basic\images\image-20260209114007768.png)

  - **本地项目上传**：直接从本地上传构建好的静态文件（HTML、CSS、JS 等）
  - **模板部署**：使用预设模板快速创建项目，如 React Web 应用模板、Vue Web 应用模板
  - **Git 仓库部署**：支持从 GitHub 等代码仓库自动拉取代码并部署

### 3.3 Zeabur

这里以创建自己的Dify服务为例，介绍Zeabur的使用流程：

1.在控制台点击”创建新项目“，弹出如下界面：

![image-20260209135603427](D:\study\AI-basic\images\image-20260209135603427.png)

- **GitHub**：可以连接到你的 GitHub 账号。绑定之后，就可以直接从 GitHub 仓库里选择项目部署（GitHub 是目前全球最大的代码托管平台）。
- **Template（模板）**：可以基于模板来部署服务。Zeabur 内置了很多预设项目模板（例如 Dify、n8n 等），你可以基于这些模板快速创建并部署应用。
- **Databases（数据库）**：用于部署数据库服务，比如 MySQL、MongoDB 等常见数据库。
- **Local Project（本地项目）**：上传一个本地文件夹，Zeabur 会自动识别其中的启动脚本。这适合将你已经在本地开发好的项目快速部署到 Zeabur 上。
- **Docker Image**：部署已经打包好的 Docker 镜像。如果你的项目已经被打成了 Docker 镜像（例如存放在 Docker Hub 或其他镜像仓库中），可以在这里直接部署。
- **Cursor**：如果你安装了 Cursor（例如 Cursor IDE），可以通过这个入口将 Cursor 中的项目直接部署到 Zeabur。

这里使用Template的方式部署Dify，选择一个Dify的版本（由不同开发者维护）：

![image-20260209140036448](D:\study\AI-basic\images\image-20260209140036448.png)

接下来输入一个任意的名称作为自定义的域名，点击部署后等待所有服务启动完成：

![image-20260209140159359](D:\study\AI-basic\images\image-20260209140159359.png)

一般来说，你只需要点击左侧的 Dify 应用，就可以看到默认的访问入口地址。但在本例中，由于前面还套了一层 nginx，你需要点击 nginx 服务来获取最终访问地址。由于**Dify是由多个服务组成的**，因此可以理解为：nginx 就是负责对外统一"收发请求"的主程序，它会把外部访问的地址分发给Dify内部各个服务。点击左侧的 Nginx，在详情页中可以看到当前的服务地址，然后在浏览器里打开这个地址，等待服务完全启动。

这里需要注意的是，部署完毕后进入自定义域名会看到一个全新的Dify界面（即使已经注册过Dify），主要的区别是：在 Zeabur 上部署的是 Dify 的**自托管实例**，与官方 cloud.dify.ai 服务是完全独立的系统，两者数据不互通。

有关端口的注意事项：默认情况下，React 应用一般会监听 3000 端口，而Zeabur 只支持监听 8080 端口的应用，因此我们需要将监听的端口进行修改。

在计算机网络中，端口可以理解为一个"逻辑通信端点"，用来区分同一台设备上运行的不同网络服务。我们平时访问网站或 IP 地址时，通常不会手动加端口号，是因为 **Web 的默认端口是 80 或 443（HTTPS）**。

什么叫做监听端口呢？简单理解就是一直在某个端口等待网络请求，一旦收到请求便转发给这个正在监听的端口。Zeabur 的平台设计决定了它只会"识别"监听 8080 端口的应用，因此无法将请求正确转发给应用（如果默认是3000端口）。

## 四、CLI开发工具

与Trea，Cursor等AI IDE不同的是，CLI AI 编程工具只能在终端中使用，在功能上两者相似，但它们通常具有更长的上下文窗口、更快的工具调用速度，并且可以兼容更多种类的大模型。

| 功能特性          | Claude Code  | Cursor         | 更优者      |
| ----------------- | ------------ | -------------- | ----------- |
| 自动任务执行      | ✅ 非常强     | ❌ 能力有限     | Claude Code |
| IDE 集成          | ❌ 仅命令行   | ✅ 原生 VS Code | Cursor      |
| 实时代码补全      | ❌ 无         | ✅ 体验极佳     | Cursor      |
| 多文件操作        | ✅ 非常强     | ⚠️ 还不错       | Claude Code |
| GitHub 一体化操作 | ✅ 可直接提交 | ⚠️ 需要手动操作 | Claude Code |
| 学习成本          | ⚠️ 中等       | ✅ 上手简单     | Cursor      |
| 上下文长度        | ✅ 非常长     | ⚠️ 较好         | Claude Code |
| 调试辅助          | ✅ 自动化     | ⚠️ 较多需手动   | Claude Code |

### 4.1 常见的CLI AI编程工具

目前主流的两大类CLI AI编程工具：

- Codex 使用 GPT-5，在整体能力上更强；
- Claude Code 通过 GLM 4.6 转发 API，整体体验接近 Claude 4，但价格更便宜。

#### 4.1.1Claude Code

Claude Code 是由 Anthropic 基于 Claude 大模型能力开发的一款 AI 编程工具。它的主要交互场景在终端，同时也支持作为 VS Code 插件来使用。类似于 AI IDE 中的 Agent，它可以深度理解开发者的代码仓库，并通过自然语言指令完成端到端的开发任务，

Claude Code优势：

- 极长的上下文窗口（可以处理完整文件甚至小型项目）
- 主动澄清模糊需求，自动规划和分配执行任务
- 对整个代码库内容的深度理解和解释能力

直接使用Claude Code会面临较大的成本，一种方式是使用”兼容Anthropic 接口”的 API（目前许多大模型厂商都支持Anthropic风格的调用方式）；另一种方式是使用 **“Claude Code Route”** 项目。它是一个开源工具，不仅支持所有常见的 API 调用接口，还允许你针对不同场景精细配置要使用的模型，并且支持对接本地部署的大模型。

这里以Kimi K2为例：

访问Kimi的官方文档，有详细说明：[在编程工具中使用 Kimi K2 模型 - Moonshot AI 开放平台 - Kimi 大模型 API 服务](https://platform.moonshot.cn/docs/guide/agent-support#macos-和-linux-2)

这里有一些基本命令：

| 命令              | 作用                                      | 示例                                     |
| ----------------- | ----------------------------------------- | ---------------------------------------- |
| claude            | 启动交互模式                              | `claude`                                 |
| claude "query"    | 执行一次性任务并输出结果                  | `claude "explain this project"`          |
| claude -p "query" | 执行一次性问题并在结束后自动退出          | `claude -p "explain this function xxxx"` |
| claude -c         | 继续最近的一次会话                        | `claude -c`                              |
| claude -r         | 恢复上一段会话                            | `claude -r`                              |
| /resume           | 在当前聊天中切换回上一段会话              | `claude -c`、`/resume`                   |
| claude commit     | 协助创建 Git 提交信息并提交代码           | `claude commit`                          |
| /init             | 用 CLAUDE.md 初始化项目说明               | `/init`                                  |
| /clear            | 清空当前会话上下文，防止信息过载          | `/clear`                                 |
| /compact          | 压缩会话历史，减少上下文 token 占用       | `/compact`                               |
| /cost             | 查看当前消费情况                          | `/cost`                                  |
| /model            | 切换使用的模型（用兼容 API 时一般可忽略） | `/model`                                 |
| /memory           | 管理 CLAUDE.md 记忆文件                   |                                          |
| /help             | 显示可用命令列表                          | `/help`                                  |
| exit or Ctrl+C    | 退出 Claude Code                          | `exit` 或 `Ctrl+C`                       |
| /agents           | 高级功能                                  |                                          |
| /mcp              | 高级功能                                  |                                          |

Claude.md：是一种提示词/配置文件，用于引导模型的生成，包括任何你希望Claude遵守的约定：

- 常用 bash 命令
- 核心文件和工具函数
- 代码风格约定
- 测试方式说明
- 仓库协作规范（例如分支命名、是用 merge 还是 rebase 等）

更多有关扩展Claude Code功能的文档可以参考：[扩展 Claude Code - Claude Code Docs](https://code.claude.com/docs/zh-CN/features-overview#claude-code)

最后简单阐释一下为什么Claude Code比集成在IDE中的编程助手更加好用（Gork进行总结）：

![image-20260209165418396](D:\study\AI-basic\images\image-20260209165418396.png)

Cursor和Trae的agent模式更像“增强版autocomplete + 半自动Composer”，容易出现scope creep（范围蔓延，AI为了让代码更好而更改提示外的其他功能）或幻觉死循环（没有严格的多轮自我验证 + 回滚机制）。但Claude Code是真正的agentic循环（Plan → Act → Verify → Fix）。

#### 4.1.2 Codex

Codex在整体能力上更加强大但成本更高，这里不再展开。

## 五、全栈开发

### 5.1 前言

从想法到产品的思维：**代码从来不是软件的出发点，而是解决方案的最后一个环节。**

在 AI 编程工具出现之前，从"想法"到"产品"之间，有一套完整且复杂的流程：

- 首先是**需求阶段**：产品经理撰写PRD（产品需求文档），组织需求评审会，PM、开发、测试一起围坐在会议室里讨论半天。

- 然后是**技术设计阶段**：后端和前端分别撰写技术方案，再组织技术评审会，甚至上下游团队的开发也要参与进来。

- 接下来是**开发阶段**：编码、单元测试、接口自测、前后端联调。每个人对着自己的屏幕敲代码，然后再凑到一起对接口。

- 然后是**测试阶段**：开发者自测通过后，"提测"交给 QA 团队，QA 进行手动测试和自动化测试，发现 Bug 再打回给开发修复。一来一回，几天就过去了。

- 再然后是**上线阶段**：代码合并、预发环境验证、灰度发布——先给 5% 用户，再 10%、50%，最后 100% 全量。每一步都小心翼翼，生怕出什么问题。

- 最后是**迭代阶段**：以两周为一个迭代周期，持续规划和交付新功能。整个流程像一台精密的机器，周而复始地运转。

AI编程工具的出现极大简化了这一套流程，以摄影的发展史类比一下：

- 以前你想拍一张像样的照片，需要懂什么？光圈、快门、对焦、ISO、白平衡……你得把这一堆复杂原理搞明白，还得买一堆昂贵的设备。大多数人光是想想就放弃了。
- 现在呢？手机相机自动帮你处理了所有技术细节。你只需要关注两件事：你想拍什么，怎么拍才好看。

当技术迭代的速度超过了人类积累经验的速度，我们应该更加关注前沿工具的使用。

同时，AI编程工具的出现虽然让我们可以关注于产品本身而非代码的底层逻辑，但我认为即使门槛降低了，对于开发者而言仍然应该掌握开发流程以引导AI更高效地向我们的想法靠齐。

### 5.2 环境搭建

#### 5.2.1 代码格式的演变

AI 有时给出 `.html` 文件，有时给出 `.ts` 文件，这是因为代码格式随着项目复杂度在演变。

一个完整的前端界面由三层组成：

![image-20260211111629527](D:\study\AI-basic\images\image-20260211111629527.png)

将三种代码写在一个 `.html` 文件里，就是**单文件格式**——双击就能运行，不需要安装任何东西：

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    /* CSS: 样式层 — 长什么样 */
    .box { background: #f0f0f0; padding: 20px; }
    .count { font-size: 24px; }
  </style>
</head>
<body>
  <!-- HTML: 结构层 — 有什么内容 -->
  <div class="box">
    <span class="count">0</span>
    <button>增加</button>
  </div>

  <script>
    /* JavaScript: 行为层 — 怎么交互 */
    let count = 0;
    document.querySelector('button').addEventListener('click', () => {
      count++;
      document.querySelector('.count').textContent = count;
    });
  </script>
</body>
</html>
```

为什么有时ctrl+s保存的网页打不开：

| 现象               | 原因                                   |
| ------------------ | -------------------------------------- |
| **完全正常**       | 单文件格式，所有代码都在一个 HTML 里   |
| **有样式但点不了** | CSS 在本地，JS 从服务器加载，断网失效  |
| **样式全无**       | CSS 和 JS 都从服务器加载，本地只有空壳 |
| **完全打不开**     | 现代单页应用，需要服务器才能运行       |

这是由于现代网站基于React/Next.js 等框架开发，**代码分离在不同文件**，内容通过 JS 动态获取，保存下来的只是一个空 HTML 壳子。

这就引出了文件组织的4种阶段：

- 单文件HTML：所有代码在一个 `.html` 文件中，只适用于概念学习

- 文件分离：结构（HTML）、样式（CSS）、逻辑（JS）分开，文件依赖关系复杂

- 模块化：使用 `import`/`export` 组织代码

- TypeScript工程化：使用 TypeScript + 构建工具，但TypeScript不能直接运行，必须经过编译(pnpm dev)：

  ```bash
  .ts/.tsx 文件 → TypeScript 编译器 → .js 文件 → 浏览器执行
  # pnpm dev命令会再开发时自动编译
  ```

因此根据项目的复杂度，提示AI使用合适的项目结构：

```bash
"生成一个单文件 HTML 的计数器" → 单文件，双击可运行
"生成一个任务管理应用" → TypeScript 项目，需要 pnpm dev
```

#### 5.2.2 技术栈

**技术栈（Tech Stack）**是开发项目时使用的技术组合。

![image-20260211113124850](D:\study\AI-basic\images\image-20260211113124850.png)

什么时候需要全栈：

- 用户系统（登录、注册、权限）
- 数据持久化（保存用户数据）
- 业务逻辑（支付、通知、邮件）

当接手一个新项目时，可以查看package.json快速了解技术栈。

#### 5.2.3 浏览器与服务器基础

**浏览器**（Chrome、Firefox、Safari）运行在用户电脑上，只能理解 HTML、CSS、JavaScript。

**服务器**是远程计算机，运行 Web 服务器软件（如 Nginx、Apache），响应浏览器请求并返回数据。

Web应用的工作流程：
![image-20260211142930873](D:\study\AI-basic\images\image-20260211142930873.png)

为什么需要node.js：ts代码需要编译才能在浏览器上运行，编译过程需要一个环境，而node.js的作用便是：

- 在你的电脑上运行构建工具
- 编译 TypeScript 为 JavaScript
- 打包代码
- 启动开发服务器

#### 5.2.4 Terminal终端

首先介绍一下终端、Shell、命令行的区别

这三个概念经常被混淆，其实层次不同：

- **终端（Terminal）**：你看到的**界面窗口**，用来输入命令。Windows 上叫 PowerShell/CMD，Mac 上叫 Terminal/iTerm2
- **Shell（壳）**：隐藏在终端后的**命令解释器**，读取你的输入并执行。常见的是 bash、zsh（Mac 默认）、PowerShell（Windows）
- **命令行（CLI）**：通过文本指令操作计算机的**操作方式**，相比图形界面更高效、更精确

什么是提示符：打开终端后，你会看到一行前面有符号的文字

```bash
user@MacBook ~ $     # Mac/Linux 的提示符是 $
PS C:\Users\user>    # Windows PowerShell 的提示符是 >
```

常见的终端快捷键：

| 快捷键     | 作用                            |
| ---------- | ------------------------------- |
| `Ctrl + C` | 停止当前运行的程序/中断当前输入 |
| `Ctrl + L` | 清屏（相当于输入 `clear`）      |
| `↑ / ↓`    | 浏览历史命令                    |
| `Tab`      | 自动补全文件名或命令            |

我们常常听到需要配置环境变量，为什么呢？

![image-20260211144457114](D:\study\AI-basic\images\image-20260211144457114.png)

实际上是你输入了一个命令，比如python，Shell会在PATH去找对应名称的可执行文件。

CLI工具为什么在实际开发中更加受欢迎：

- 没有图形化界面，运行更高效
- 无需点击，可以直接用参数控制行为
- 可以写成脚本自动批量执行

命令行的参数有两种格式：

- **短参数**：一个减号加字母，如 `-v`（version）、`-h`（help），更快捷
- **长参数**：两个减号加单词，如 `--version`、`--help`，更易读

同时运行多个命令：

1.使用&&连接命令，**前一个执行成功才会执行下一个**：

```bash
# 清理并重新安装
rm -rf node_modules && pnpm install
```

2.使用；（或换行）连接命令，**无论前一个是否成功都会执行下一个**：

```bash
mkdir new-folder ; cd new-folder    # new-folder 是示例文件夹名
```

#### 5.2.5 包管理器

可以类比Python的Conda生态：

![image-20260211145914601](D:\study\AI-basic\images\image-20260211145914601.png)

```bash
# 项目A：需要Python 3.8（老库只支持3.8）
conda activate py38
pip install tensorflow==2.4

# 项目B：需要Python 3.10（用最新特性）
conda activate py310
pip install torch

# --------------------------------------------------

# 项目X：需要Node 14（老项目）
nvm use 14
npm install

# 项目Y：需要Node 20（用新ES2023特性）
nvm use 20
npm install
```

**Node.js** 是 JavaScript 运行时环境，让 JS 能在服务器端运行。现代前端构建工具都依赖它。

**LTS**（Long Term Support）是长期支持版本，比 Current 更稳定，推荐用于开发。

**nvm**（Node Version Manager）让你在同一台电脑上安装和切换多个 Node.js 版本。

**pnpm** 是包管理器，采用链接模式，用于安装项目依赖。相比 npm的复制模式，它**更快、更节省磁盘空间**。

常用命令：

| 命令              | 作用                                          |
| ----------------- | --------------------------------------------- |
| `pnpm init`       | 初始化项目                                    |
| `pnpm install`    | 安装所有依赖                                  |
| `pnpm add xxx`    | 安装生产依赖（xxx 替换为包名，如 React）      |
| `pnpm add -D xxx` | 安装开发依赖（xxx 替换为包名，如 TypeScript） |
| `pnpm remove xxx` | 卸载包                                        |
| `pnpm dev`        | 运行脚本（等同于 pnpm run dev）               |

除了安装项目依赖，还可以用npm 安装**全局工具**——这些工具可以在电脑的任何位置使用：

```bash
# 安装全局 CLI 工具
npm install -g @anthropic-ai/claude-code # pnpm没有这个用法
```

tips：npm 凭借其广泛的普及度和良好的兼容性，在小型项目和对兼容性要求高的场景中表现出色；而 pnpm 则以高效的安装速度、节省磁盘空间以及对 Monorepo 的强大支持，在大型项目和追求极致性能的场景中更胜一筹。

每个项目可能依赖不同的三方库，我们需要告诉机器下载的依赖名称和版本。

我们使用配置文件（package.json）来对项目的依赖进行记录，使用`pnpm install` 会根据配置自动下载所有依赖。同时还有pnom-lock.yaml文件，可以记录每个依赖的精确版本，会自动生成。

#### 5.2.6 创建项目

由于之前没有接触过基于Next.js的项目，所以这里需要重新学习一下~

现代框架都提供了官方**脚手架**，一条命令即可创建标准项目。

```bash
# 创建 Next.js 项目（my-app 是项目名，可以改）
pnpm create next-app@latest my-app

# 创建 Vite + React 项目
pnpm create vite@latest my-app -- --template react
```

创建时会询问配置选项：

![image-20260211153427883](D:\study\AI-basic\images\image-20260211153427883.png)

对于一个相对完整的Next.js项目，应该有如下结构：

```bash
my-next-app/
├── src/
│   ├── app/                 # 页面和 API
│   │   ├── page.tsx         # 首页
│   │   ├── layout.tsx       # 全局布局
│   │   └── api/             # API 接口
│   │
│   ├── components/          # UI 组件
│   └── lib/                 # 工具函数
│
├── public/                  # 静态资源（图片、字体）
├── package.json             # 依赖管理
└── tsconfig.json            # TypeScript 配置
```

让AI创建一个页面时，会在`src/app/` 下创建一个xxx.tsx;创建一个组件时，会在`src/components/` 下创建一个xxx.tsx。

我们在运行一个项目时常常会遇到端口被占用的报错提示，比如：

```bash
Error: listen EADDRINUSE: address already in use :::3000
```

这时可以手动指定端口：

```bash
# Next.js：指定端口启动
pnpm dev -- -p 3001
```

也可以用netstat查看占用端口的进程然后kill，但是一般不这样做，换一个不常用的端口即可，比如在端口前加1。

tips：服务会占用一个端口，在生产环境中常常使用Nginx反向代理，用户访问 `https://example.com` 时，请求先到 Nginx（监听 80/443 端口），然后 Nginx 转发给你的服务端口。

### 5.3 AI 能力

