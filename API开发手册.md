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

![image1.kHLtXFq8](.\images\image1.kHLtXFq8.png)

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

  ![image-20260209113909909](.\images\image-20260209113909909.png)

  支持多种部署模板：

  ![image-20260209114007768](.\images\image-20260209114007768.png)

  - **本地项目上传**：直接从本地上传构建好的静态文件（HTML、CSS、JS 等）
  - **模板部署**：使用预设模板快速创建项目，如 React Web 应用模板、Vue Web 应用模板
  - **Git 仓库部署**：支持从 GitHub 等代码仓库自动拉取代码并部署

### 3.3 Zeabur

这里以创建自己的Dify服务为例，介绍Zeabur的使用流程：

1.在控制台点击”创建新项目“，弹出如下界面：

![image-20260209135603427](.\images\image-20260209135603427.png)

- **GitHub**：可以连接到你的 GitHub 账号。绑定之后，就可以直接从 GitHub 仓库里选择项目部署（GitHub 是目前全球最大的代码托管平台）。
- **Template（模板）**：可以基于模板来部署服务。Zeabur 内置了很多预设项目模板（例如 Dify、n8n 等），你可以基于这些模板快速创建并部署应用。
- **Databases（数据库）**：用于部署数据库服务，比如 MySQL、MongoDB 等常见数据库。
- **Local Project（本地项目）**：上传一个本地文件夹，Zeabur 会自动识别其中的启动脚本。这适合将你已经在本地开发好的项目快速部署到 Zeabur 上。
- **Docker Image**：部署已经打包好的 Docker 镜像。如果你的项目已经被打成了 Docker 镜像（例如存放在 Docker Hub 或其他镜像仓库中），可以在这里直接部署。
- **Cursor**：如果你安装了 Cursor（例如 Cursor IDE），可以通过这个入口将 Cursor 中的项目直接部署到 Zeabur。

这里使用Template的方式部署Dify，选择一个Dify的版本（由不同开发者维护）：

![image-20260209140036448](.\images\image-20260209140036448.png)

接下来输入一个任意的名称作为自定义的域名，点击部署后等待所有服务启动完成：

![image-20260209140159359](.\images\image-20260209140159359.png)

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

![image-20260209165418396](.\images\image-20260209165418396.png)

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

![image-20260211111629527](.\images\image-20260211111629527.png)

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

![image-20260211113124850](.\images\image-20260211113124850.png)

什么时候需要全栈：

- 用户系统（登录、注册、权限）
- 数据持久化（保存用户数据）
- 业务逻辑（支付、通知、邮件）

当接手一个新项目时，可以查看package.json快速了解技术栈。

#### 5.2.3 浏览器与服务器基础

**浏览器**（Chrome、Firefox、Safari）运行在用户电脑上，只能理解 HTML、CSS、JavaScript。

**服务器**是远程计算机，运行 Web 服务器软件（如 Nginx、Apache），响应浏览器请求并返回数据。

Web应用的工作流程：
![image-20260211142930873](.\images\image-20260211142930873.png)

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

![image-20260211144457114](.\images\image-20260211144457114.png)

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

![image-20260211145914601](.\images\image-20260211145914601.png)

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

![image-20260211153427883](.\images\image-20260211153427883.png)

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

## 六、Agent开发实战

### 6.0 智能体的构成与运行原理

智能体的核心理念是：**感知-思考-行动-观察**，智能体就是在一次次这样的循环中运行，这一机制被称为**智能体循环（Agent Loop）**。

![image-20260227160114276](images/image-20260227160114276.png)

- 感知（Perception）：循环的起点，通过“传感器”收集来自环境的信息，这里的信息可以来自初始输入也可以是上一步循环的结果
- 思考（Thought）：智能体感知环境信息后，自主思考和规划：
  - 规划（Planning）：基于当前感知以及内部记忆，制定或更新行动计划
  - 工具调用（Tool Selection）：根据规划，从可用的根据库中选择合适的工具进入执行过程
- 行动（Action）：智能体结合规划以及选择的工具行动，改变环境状态

这里讨论一下Workflow和Agent的差异：

简单来说，Workflow是在人为圈定的范围内让AI执行，Agent则赋予AI自由度期望其**自主完成**任务。

### 6.1 基于低代码平台的智能体搭建

对于一个快速发展的领域而言，纯代码的开发模式并非总是最高效的选择，尤其是在需要快速验证想法或构建原型时，因此这时我们可以选择低代码平台。

低代码平台的主要优势体现在：

- 降低技术门槛提升效率，低代码平台将复杂的技术细节（如 API 调用、状态管理、并发控制）封装成一个个易于理解的“节点”或“模块”。用户通过拖拽连接的方式即可构建工作流
- 数据流转可视化，你可以清晰地看到数据在每一个节点之间如何流动
- 标准化功能模块，主流的低代码平台内置了业内的最佳实践，比如可以在embedding时直接调用三方API服务，标准化工具调用规范等等

目前主流的低代码开发平台包括：Coze，Dify，n8n...

- Coze: 拥有极其友好的可视化界面，用户可以像搭建乐高积木一样，通过拖拽插件、配置知识库和设定工作流来创建智能体，内置丰富的插件，可一键发布到主流社交媒体平台
- Dify：企业级开发运营平台，支持私有化部署，插件完善
- n8n:工作流自动化工具，主要是将各类服务，数据库连接，API调用组合成自动化业务流程，可以在其中嵌入AI能力，特点是高度定制化

#### 6.1.1 基于Coze构建“每日AI简报”助手

功能介绍：该智能体能够自动化地从多个信息源（包括36氪、虎嗅、it之家、infoq、GitHub、arXiv）抓取当日最新的AI领域头条新闻、学术论文及开源项目动态，并将其结构化、专业化地整合成一份生动、精炼的简报。

我们先来思考一下该智能体的主要工作流程：接入权威信息来源-->自动抓取AI相关的内容-->整合并结构化内容-->输出简报。

这是一个简单的流程，核心目标其实是抓取数据，在低代码平台已经内置了相关功能，我们下载对应插件即可。

这里使用RSS插件，实现信息抓取和解析订阅源的内容，之后让大模型进行分析和整合。

```bash
RSS 是网站以 XML/JSON 格式发布的内容更新清单（如新闻、博客、学术论文），插件通过订阅这些 RSS 地址，定时拉取最新条目并结构化输出，供智能体使用。
```

接下来根据插件的提示和要求配置相关参数：

![image-20260226113103223](images/image-20260226113103223.png)



接下来我们只需要拖拽相关模块即可完成目标，注意数据在不同节点中的流转形式：

![image-20260226172340788](images/image-20260226172340788.png)

我们在大模型节点中添加系统提示词以及用户提示词，引导模型输出我们想要的风格与内容：

```te
# 角色
你是一位资深且权威的科技媒体编辑,擅长高效精准地整合并创作极具专业性的科技简报,特别在AI领域的技术动态、前
沿学术研究成果及热门开源项目方面拥有深入的分析与整合能力。
## 工作流
### 日报输出格式
1. 日报开头显著标注“AI日报”、当天日期，例如：“AI日报 | 2025年9月24日”。
2. <!!!important!!!> 根据每则AI技术新闻、每篇AI学术论文、每个AI开源项目的不同内容，在其标题开头添加
一个独有的Emoji表情符号。
3. 输出的所有内容必须与AI、LLM、AIGC、大模型等技术主题高度相关，坚决排除任何无关信息、广告及营销类内
容。
4. 必须为每一条目（包括AI技术新闻、AI学术论文、AI开源项目）提供其对应的原始链接。
5. 对输出的每一条新闻或项目，都进行一个简短、精准的概况描述。


- **信息提取与整合：** 从输入源 `{{input_1}}`、`{{input_2}}`、`{{input_3}}` 和
`{{input_4}}` 中，筛选并提取关于AI、大模型、AIGC、LLM等相关主题的文章标题及其对应链接，整理为**“AI
技术新闻”**模块。
- **学术论文摘要：** 从输入源 `{{input_5}}` 中，根据字段 `arxiv_title` 和 `arxiv_link`，总结并整理
最新的论文内容，形成**“AI学术论文”**模块。
- **开源项目筛选：** 从输入源 `{{input_6}}` 中，筛选出最受瞩目且具影响力的**5个AI开源项目**。提取这些
项目的标题和对应链接，整理为**“AI开源项目”**模块。
# 注意事项（Attention）
- 严格遵循系统提示中定义的日报输出格式。
```

由于目前Coze的更新，在智能体中导入工作流需要升级付费，这里仅在试运行中看效果：

![image-20260226173111015](images/image-20260226173111015.png)

从上图的运行结果可以发现，输出一次消耗的token达到了45k，这可能与我们传给LLM的参数过于繁杂，比如输入一个网址，插件的返回除了相关内容外，可能还包括git_url，git_commit_url等与任务关联度不高的参数，这些参数可能导致token消耗急剧上升。

正如上文提到，Coze只有付费功能才能导出工作流，且导出格式为zip而非dify，n8n的json格式，这是它的一大局限性。

#### 6.1.2 Dify

基于大模型的聊天机器人只能进行思考并给出建议，并不能做事，难以日你个如真实业务流程。

为了能够让AI真正融入业务场景，实现自动化升级，我们需要赋予它三项核心能力：

- 专属知识——让它能够通读并了解你的产品文档、客户资料、内部制度；
- 工具调用（或者叫插件）——让它能操作数据库、调用 API；
- 结构化执行——让它按预设逻辑一步步完成任务，而非自由发挥。

**注：当前业界所说的简单版本的“智能体”，大多指基于 LLM + 工具 + 知识库组合而成的增强型应用，并非所谓能够自主规划的智能体。不具备真正的推理与长期规划能力。**

##### 6.1.2.1 基于知识库的问答机器人

事实上，在大量实际业务场景中，用户的核心诉求并非让 AI 自动执行复杂操作（如调用 API 或跨系统协调任务），而是希望它能基于企业自身的专属资料，提供精准、可靠的问答支持。

关键突破在于：**让模型的回答有理有据。**

RAG 的基本思路是：在用户提问时，系统首先从企业知识库中检索出与问题语义最相关的若干文本片段（例如产品手册中的某一段、HR制度中的某一条款），然后将这些片段作为上下文“注入”到大模型的输入中，引导它基于真实资料生成回答。**这样可以圈定模型的回答范围，通过外部知识的注入，提升模型在特定任务下的专业性和准确性。**

核心任务：将静态分散的文档资料转换为精确而结构化的知识库供模型参考。

##### 6.1.2.2 AI工作流

仅依赖大模型的自由推理，容易遗漏关键信息并且无法满足企业可审计，可复用，可监控的标准，引出更高阶的AI应用范式：AI工作流。

工作流指的是：**将一个复杂任务拆解为多个子步骤，并通过可视化或代码方式进行编排，将AI能力SOP化（标准化操作流程）。**

目前市面上的低代码 AI 工作流平台选择丰富。尽管 AWS、Azure、阿里云等主流云厂商均推出了相应的 AI 工作流解决方案，但 Dify、Coze 和 n8n 凭借以下三大核心优势，成为当前应用最广泛的代表：

1. 极致易用性。平台采用可视化拖拽式界面设计，用户无需深入理解底层技术，即可快速上手。
2. 高灵活性。支持自定义组件与扩展 API 接口，既能适应教学演示、MVP（最小可行产品）验证等轻量场景，也能满足中小型团队的敏捷迭代需求。
3. 成熟生态。不仅官方文档详尽、响应及时，还拥有活跃的用户社区，便于快速获取来自不同用户的预设方案。

Dify：知识库功能突出，能处理多种格式文档并进行高效的向量检索，兼容各种模型；开源并支持私有化部署。

Coze：易用性为核心的云服务平台，丰富的生态和插件库，第使用门槛，但私有化部署能力有限。

n8n：可编程工作流自动化平台，其核心定位是连接各类应用、数据库与API，实现数据流动与任务自动化执行。

其他：百度千帆，阿里云百炼，腾讯TI，适合已经处于相应厂商云生态的企业。

##### 6.1.2.3 什么是Dify

Dify 是一个用于开发 LLM 应用的开源平台。它提供了直观的界面，将 **Agent 工作流、RAG 流水线、工具能力、模型管理、可观测性**等功能结合在一起，帮助你快速地从原型走向生产环境。

支持自定义模型供应商

为满足特定业务需求、成本控制或数据隐私要求，我们常常需要接入自定义模型。Dify 支持配置三类核心模型：**大语言模型（LLM）、Embedding 模型和 Rerank 模型。**

##### 6.1.2.4 Dify知识库

在Dify的知识库界面创建自己的知识库。

知识库设置界面：

- 文本切分规则，主要关注 **maximum chunk length（最大切分长度）** 。可以尝试设置为 512、2048 或 4096，然后点击预览观察不同设置下的效果。
- **Chunk overlap（切片重叠）** 选项。它决定相邻片段之间是否会保留一部分重叠内容。适当的重叠有助于避免重要信息被拆到不同片段而难以理解。
- **Chunk using Q&A format in English** 。启用后，系统会使用大语言模型，将知识库的一部分内容转换成问答形式来存储，这在某些场景下可以显著提升检索效果。
- **Embedding** ，是把非结构化数据（例如文本、图片等）转换成计算机能够理解的“数字向量”（Embedding 向量）。通过这种转换，模型能够快速计算不同数据之间的相似度，从而实现语义相近内容的匹配，比如根据用户输入的一句话，找到语义最接近的文档、图片或商品。
- **Rerank model**，对“初步筛选出的候选结果”进行二次、更精细的排序，让和用户需求最匹配的结果排在更靠前的位置，从而显著提升最终结果的相关性和用户体验。

检索设置：

- **Top K** 指的是向量检索时，返回与查询向量最相似的前 K 个文本切片数量。
- **Score Threshold** 则是一个“得分阈值”：只有相似度得分大于或等于该阈值的文本片段才会被返回。这样可以过滤掉相关度较低的内容，让结果更加准确。

大模型挂载了知识库后，

在每一轮对话中，你都可以在回答中看到被命中的知识库来源。点击对应条目即可查看检索到的具体文本片段。

##### 6.1.2.5 工作流的导入与导出

Dify 支持通过 DSL（Domain Specific Language） 格式导入和导出工作流。DSL 是一种基于 JSON 的标准化描述方式，能够完整保留工作流的节点结构、连接关系和配置参数。

3.5 创建Dify Workflow应用

工作流是Dify将复杂业务逻辑可视化的核心方式，通过它你可以像搭积木一样构建智能流程。

![image-20260205112557167](.\images\image-20260205112557167.png)

Chatflow 专为对话而设计。它模拟一个具有记忆和上下文理解能力的对话者，非常适合需要多轮交互、状态维持的场景。

Workflow 则专注于流程的自动化执行。它像一条预设的流水线，擅长处理一次性输入、多步骤处理、并产生确定性输出的任务。

为避免选型错误带来的效率低下，你可以通过四个关键问题来审视你的任务需求：

1. 任务过程是否需要依赖多次的用户输入与调整？
2. 结果的呈现是否需要分步骤、流式地进行？
3. 处理逻辑是否严重依赖于之前的交互历史？
4. 任务是否由事件触发，且输入输出多为一次性完成？

如果前三个问题的答案为“是”，那么 Chatflow 是理想选择，典型场景包括智能客服、教育辅导、创意协作等。如果第四个问题特征显著，则应选用 Workflow，它更适用于数据清洗、报表生成、批量处理等自动化场景。

![image-20260205112938391](.\images\image-20260205112938391.png)

整个界面的核心是中央的编辑画布，你将以可视化方式在这里构建应用逻辑。如图所示，一个基础的工作流通常始于 START 节点（用于接收输入），经由连线将数据传递至 LLM 节点进行处理，最终通过 ANSWER 节点输出结果。每个节点代表一个功能模块，而连线则决定了任务执行的顺序。

左侧面板主要用于应用管理功能，分别是流程编排，获取集成凭证，日志以及性能监控。

##### 6.1.2.6 常见节点

在Dify中内置了许多功能的节点和工具插件：

![image-20260205133159018](.\images\image-20260205133159018.png)

- LLM节点：核心计算单元，用于调用大语言模型。其配置重点在于**提示词工程与参数调优**，将业务问题转化为模型的执行指令。
- Knowledge Retrieval 节点：知识检索单元，负责从预设知识库、外部权威数据源中检索与业务问题相关的信息，为 LLM 节点提供精准的知识支撑，帮助减少大语言模型输出的 “幻觉” 问题。
- Answer 节点：结果输出单元，负责接收 LLM 处理后的内容，将其整理为符合业务场景需求的最终成果形式。其配置重点在于**输出格式的定义**（如话术模板、排版规范）。
- Agent节点：高阶决策单元。它不仅调用模型，还可实施多步骤规划、自主选择并调用外部工具，适用于需要**动态决策**的复杂任务链。
- Question Classifier 节点：问题分类单元，负责对输入的业务问题进行类型识别与归类（比如按问题意图、主题领域等维度划分），帮助后续流程精准匹配对应的处理节点（如不同类型的问题适配不同的 LLM 提示词或工具链）。
- Code 节点：代码处理单元，负责在工作流中执行**自定义代码逻辑**，可实现数据格式转换、复杂计算等个性化处理需求。其配置重点在于代码语法的正确性与执行环境的适配。
- Template 节点：模板处理单元，负责将动态数据填充至预设模板中，生成符合格式要求的内容（如定制化文案、报告框架）。其配置重点在于模板语法的编写与变量映射规则的设置。
- Variable Aggregator 节点：变量聚合单元，负责收集工作流中多个节点输出的变量数据，将分散的变量整合为统一数据集。其配置重点在于聚合的变量范围与数据合并规则的定义。
- Doc Extractor 节点：文档提取单元，负责从 PDF、Word 等各类文档中提取文本、表格等关键内容，转化为工作流可处理的结构化数据。其配置重点在于**文档类型的适配与提取内容的筛选规则**。
- Variable Assigner 节点：变量赋值单元，负责定义、初始化或更新工作流中的变量，为流程内的数据传递提供载体。其配置重点在于变量的命名、数据类型及赋值逻辑的设定。
- Parameter Extractor 节点：参数提取单元，负责从用户请求、接口返回等输入内容中提取指定参数，将非结构化信息转化为结构化数据。其配置重点在于提取规则（如正则表达式、JSON 路径）的配置。
- HTTP Request 节点：HTTP 请求单元，负责向外部系统接口发起 HTTP 请求（含 GET、POST 等方法），实现工作流与外部服务的数据交互。其配置重点在于请求地址、请求方法及参数 /headers 的设置。
- List Operator 节点：列表操作单元，负责对数组、列表类型的数据进行处理（如过滤、排序、拆分），调整数据结构以适配后续流程。其配置重点在于操作类型（如过滤条件、排序规则）的定义。

![image-20260205133217450](.\images\image-20260205133217450.png)

在 Dify 中，大部分工具都可以直接作为节点放在画布上，像其他节点一样被上下游连线，只要你提供的输入符合该节点（工具）的参数规范，它就能正常执行并产出可继续流转的结果。

常用的工具：

- 网络搜索工具 以 Tavily Search 为代表，为大模型提供面向 AI 优化的**实时检索能力**。 它会返回结构化的搜索结果（如标题、摘要、链接等），可以直接作为 LLM 提示词的一部分。
- 数据处理工具 例如 JSON Process 插件，用于对 JSON 数据进行查询、筛选、转换、合并等高级操作。 在处理复杂 API 响应或多层嵌套数据时，你可以将“数据清洗 + 重组”的逻辑交给该工具，从而简化在 Code 节点中频繁手写解析代码的工作。
- 格式处理工具 如 Markdown Exporter，可以将生成内容按指定格式导出，例如 Markdown 文档、特定排版模板等，方便后续用于展示、汇报或集成到其他系统。

##### 6.1.2.7 案例：super-Agent个人助手

我们使用Dify构建一个功能更加强大的个人助手，涵盖日常问答、文案优化、多模态生成、数据分析等多个场景。

正在制作中...

##### 6.1.2.8 Dify的局限性分析

Dify是一款一站式开发平台，拥有完善的社区生态，主要的劣势体现在性能瓶颈（核心服务由Python语言实现，性能相对较差）以及使用成本上。

### 6.2 智能体经典范式

在6.1中曾经提到，智能体的运行原理是“感知-思考-行动-观察”，但由于LLM本身属于开放域，容易产生幻觉，因此产生了许多经典的架构范式，组织智能体实现正确循环。

最具代表性的三种范式：

-  ReAct：“**思考+行动**”的范式组织，让智能体边思考边行动
- Plan-and-Solve：引导智能体根据任务目标先进行**全局规划**，指定完整计划后再行动
- Reflection：赋予智能体“**反思**”的能力，在行动的过程中**自我纠错**

#### 6.2.1 ReAct

ReAct范式核心思想是模仿人类的解决问题方式，由Shunyu Yao于2022年提出；在该框架提出前，主流方法分为两派，分别是“纯思考”以及“纯行动”，这两种方式有着顾名思义的局限性。

ReAct通过**提示词工程**将两者巧妙地结合起来：

- Thought：智能体大脑的思考过程，主要负责感知以及思考
- Action：智能体的具体行动，通常是调用工具
- Observation：执行结果以及上一步行动的状态

当智能体在Thought中认为已经解决了问题，便停止循环并输出结果。

![image-20260302092100576](images/image-20260302092100576.png)

适用场景（**涉及外部知识的获取与外部环境的交互**）：

- 需要外部知识的任务：如查询实时信息（天气、新闻、股价）、搜索专业领域的知识等
- 需要精确计算的任务：将数学问题交给计算器工具，避免LLM的计算错误 
- 需要与API交互的任务：如操作数据库、调用某个服务的API来完成特定功能

这里给出完整的提示词模版：

```bash
# ReAct 提示词模板
REACT_PROMPT_TEMPLATE = """
请注意，你是一个有能力调用外部工具的智能助手。
可用工具如下:
{tools}
请严格按照以下格式进行回应:
Thought: 你的思考过程，用于分析问题、拆解任务和规划下一步行动。
Action: 你决定采取的行动，必须是以下格式之一:
- `{{tool_name}}[{{tool_input}}]`:调用一个可用工具。
- `Finish[最终答案]`:当你认为已经获得最终答案时。
- 当你收集到足够的信息，能够回答用户的最终问题时，你必须在Action:字段后使用 Finish[最终答案] 来输
出最终答案。
现在，请开始解决以下问题:
Question: {question}
History: {history}
"""
```

其中最重要的是**格式规范**，便于我们后续使用代码解析输出的内容。

ReAct在每一步都能输出其思考过程以及执行的工具，让我们更好地掌控整体流程；其局限性体现在：

- 高度依赖底层LLM的综合能力，该范式对于模型的规划推理以及遵循指令的能力有较高要求
- 执行效率问题，一个任务通常需要多次调用LLM，每一次都伴随token以及时间成本
- 提示词脆弱性，ReAct的成功很大程度上体现于提示词的设计，提示词微小的改动可能导致LLM巨大的行为差异（**这里就体现出格式规范的重要性，调试时注意模型输出的格式**）

#### 6.2.2 Plan-and-Solve

Plan-and-Solve Prompting 由 Lei Wang 在2023年提出[2]。其核心动机是为了解决思维链在处理多步骤、复 杂问题时容易“偏离轨道”的问题。基于该范式设计的智能体更加聚焦于全局的视角，区别于ReAct思考与行动融合，该范式将流程解耦为两个阶段：

- 规划：根据用户问题进行规划，制定一个清晰的计划
- 执行：得到完整计划后，严格按照计划执行，得到最终答案

![image-20260302095647554](images/image-20260302095647554.png)

Plan-and-Solve 尤其适用于那些结构性强、可以被清晰分解的复杂任务，例如： 

- 多步数学应用题：需要先列出计算步骤，再逐一求解。 
- 需要整合多个信息源的报告撰写：需要先规划好报告结构（引言、数据来源A、数据来源B、总结）， 再逐一填充内容。 
- 代码生成任务：需要先构思好函数、类和模块的结构，再逐一实现。

提示词模版示例：

````bash
PLANNER_PROMPT_TEMPLATE = """
你是一个顶级的AI规划专家。你的任务是将用户提出的复杂问题分解成一个由多个简单步骤组成的行动计划。
请确保计划中的每个步骤都是一个独立的、可执行的子任务，并且严格按照逻辑顺序排列。
你的输出必须是一个Python列表，其中每个元素都是一个描述子任务的字符串。
问题: {question}
请严格按照以下格式输出你的计划,```python与```作为前后缀是必要的:
```python
["步骤1", "步骤2", "步骤3", ...]
```
"""

EXECUTOR_PROMPT_TEMPLATE = """
你是一位顶级的AI执行专家。你的任务是严格按照给定的计划，一步步地解决问题。
你将收到原始问题、完整的计划、以及到目前为止已经完成的步骤和结果。
请你专注于解决“当前步骤”，并仅输出该步骤的最终答案，不要输出任何额外的解释或对话。
# 原始问题:
{question}
# 完整计划:
{plan}
# 历史步骤与结果:
{history}
# 当前步骤:
{current_step}
请仅输出针对“当前步骤”的回答:
"""
````

#### 6.2.3 Reflection

ReAct 和 Plan-and-Solve 范式中，智能体一旦完成了任务，其工作流程便告结束。然而，它们生成的初始答案，无论是行动轨迹还是最终结果，都可能存在谬误或有待改进之处。Reflection范式引入了自我校正机制，让智能体发现执行过程中的不足并改进。

核心工作流程：：执行 -> 反思 -> 优化

- 执行 (Execution)：首先，智能体使用我们熟悉的方法（如 ReAct 或 Plan-and-Solve）尝试完成任务， 生成一个初步的解决方案或行动轨迹。这可以看作是“初稿”。
- 反思 (Reflection)：接着，智能体进入反思阶段。它会调用一个**独立的、或者带有特殊提示词的大语言模型**实例，来扮演一个“评审员”的角色，对“初稿”做一个“反馈”。
-  优化 (Refinement)：最后，智能体将“初稿”和“反馈”作为新的上下文，再次调用大语言模型，要求它根 据反馈内容对初稿进行修正，生成一个更完善的“修订稿”。

![image-20260302101229699](images/image-20260302101229699.png)

由于需要基于历史信息做反思和优化，所以引入了**短期记忆模块**，帮助智能体记忆解决过程。

提示词示例：

````bash
1.初始提示词
INITIAL_PROMPT_TEMPLATE = """
你是一位资深的Python程序员。请根据以下要求，编写一个Python函数。
你的代码必须包含完整的函数签名、文档字符串，并遵循PEP 8编码规范。
要求: {task}
请直接输出代码，不要包含任何额外的解释。
"""

2.反思提示词
REFLECT_PROMPT_TEMPLATE = """
你是一位极其严格的代码评审专家和资深算法工程师，对代码的性能有极致的要求。
你的任务是审查以下Python代码，并专注于找出其在<strong>算法效率</strong>上的主要瓶颈。
# 原始任务:
{task}
# 待审查的代码:
```python
{code}
```
"""
请分析该代码的时间复杂度，并思考是否存在一种<strong>算法上更优</strong>的解决方案来显著提升性能。
如果存在，请清晰地指出当前算法的不足，并提出具体的、可行的改进算法建议（例如，使用筛法替代试除法）。
如果代码在算法层面已经达到最优，才能回答“无需改进”。
请直接输出你的反馈，不要包含任何额外的解释。

3.优化提示词
REFINE_PROMPT_TEMPLATE = """
你是一位资深的Python程序员。你正在根据一位代码评审专家的反馈来优化你的代码。
# 原始任务:
{task}
# 你上一轮尝试的代码:
{last_code_attempt}
评审员的反馈：
{feedback}
请根据评审员的反馈，生成一个优化后的新版本代码。
你的代码必须包含完整的函数签名、文档字符串，并遵循PEP 8编码规范。
请直接输出优化后的代码，不要包含任何额外的解释。
"""
````

Reflection范式从机制上引导模型高准确性，可靠性的回答，但会面临**token消耗，提示词工程复杂度以及延迟的显著提升**。

### 6.3 框架开发实践

本章讨论目前业界主流的智能体框架，构建高效规范的智能体应用。

#### 6.3.1 为什么使用框架 

使用框架进行开发的主要价值：

- 提升代码的复用率以及开发效率，一个框架会提供一个通用的基类，封装了智能体的核心运行逻辑，不再需要重复构建
- 实现**核心组件的解耦**，智能体系统本身是由多个模块共同耦合而成的，框架可以让我们专注于特定模块的开发，使得整体流程更加清晰可控，便于维护
- 标准化状态管理，在实际运行的智能体应用中，状态管理十分重要，需要同时处理**上下文窗口限制、历史信息持久化、多轮对话状态**跟踪等问题
- 简化调试过程，例如为了观测智能体内部的执行流程，我们可以引入**事件回调机制**，在关键节点自动触发上报，比手动print更加高效优雅

#### 6.3.2 主流框架

- AutoGen：AutoGen 的核心思想是通过**对话实现协作**。它将多智能体系统抽象为一个由多个“可对话”智能体组成的群聊。开发者可以定义不同角色（如  Coder ,  ProductManager ,  它们之间的交互规则（例如， Coder 写完代码后由  Tester ），并设定 Tester 自动接管）。任务的解决过程，就是这些 智能体在群聊中通过自动化消息传递，不断对话、协作、迭代直至最终目标达成的过程。
-  AgentScope：AgentScope 是一个专为**多智能体应用**设计的、功能全面的开发平台。它的核心特点 是易用性和工程化。它提供了一套非常友好的编程接口，让开发者可以轻松定义智能体、构建通信网 络，并管理整个应用的生命周期。其内置的消息传递机制和对分布式部署的支持，使其非常适合构建和 运维复杂、大规模的多智能体系统。
- CAMEL：CAMEL 提供了一种新颖的、名为**角色扮演** (Role-Playing) 的协作方法。其核心理念是， 我们只需要为两个智能体（例如， AI研究员 和  Python程序员）设定好各自的角色和共同的任务目标， 它们就能在“初始提示 (Inception Prompting)”的引导下，自主地进行多轮对话，相互启发、相互配 合，共同完成任务。它极大地降低了设计多智能体对话流程的复杂度。 
- LangGraph：作为 LangChain 生态的扩展，LangGraph 另辟蹊径，将智能体的执行流程建模为图  (Graph)。在传统的链式结构中，信息只能单向流动。而 LangGraph 将每一步操作（如调用LLM、 执行工具）定义为图中的一个节点 (Node)，并用边 (Edge) 来定义节点之间的跳转逻辑。这种设计**天然支持循环 (Cycles)**，使得实现如 Reflection 这样的迭代、修正、自我反思的复杂工作流变得异常简单和直观。

框架种类十分繁多，以上列举的被称为智能体框架，它们有各自的适用场景，在这里重点学习第一代通用LLM应用框架LangChain。

#### 6.3.3 LangChain

##### 6.3.3.1 核心概念

LangChain 的核心是将 **大语言模型**（如 GPT）与 **外部工具**、**数据库**、**API** 等集成，帮助你处理多轮对话、数据查询、信息检索等任务。

- **链（Chains）**：组合不同步骤（如文本生成、查询数据库等）形成一个处理流程。你需要理解如何创建、组合和调整链（`LLMChain`, `SimpleChain`, `MapReduceChain` 等）。
- **工具（Tools）**：LangChain 提供了将外部工具（如数据库查询、API 调用、文件读取等）与语言模型结合的功能。
- **代理（Agents）**：代理是一个更高层次的构建，能够根据用户输入和环境动态选择并调用工具。
- **记忆（Memory）**：LangChain 可以管理对话的上下文信息，支持 **对话记忆** 和 **状态管理**，非常适合多轮对话和上下文感知的应用。

LangChain的核心模块：

- **`LLMChain`**：核心类，用于串联模型和外部输入输出的处理链。掌握 `LLMChain` 是基本功。
- **`PromptTemplate`**：与模型交互时的 **提示模板**，用来组织和格式化输入文本。
- **`AgentExecutor`**：用于动态选择和执行工具的类。理解代理的工作原理对于开发更复杂的交互式应用至关重要。
- **`Tool` 和 `tool decorator`**：创建外部工具，并将它们与 LangChain 集成。工具可以是数据库查询、API 调用、文件操作等。

##### 6.3.3.2 快速入门

这里以一个简单的Agent为例，对LangChain的基本功能快速入门。

1.安装相关依赖，这里用python自带的venv创建虚拟环境

```bash
python -m venv lc_demo # 创建一个名为lc_demo的虚拟环境
./lc_demo/Scripts/activate # 激活虚拟环境
cd .....
uv init # 项目初始化
uv add langchain
uv add langchain-openai # 安装OpenAI集成
```

2.首先定义系统提示词

```python
# 定义系统提示
SYSTEM_PROMPT = """你是一位擅长用双关语表达的专家天气预报员。

你可以使用两个工具：

- get_weather_for_location：用于获取特定地点的天气
- get_user_location：用于获取用户的位置

如果用户询问天气，请确保你知道具体位置。如果从问题中可以判断他们指的是自己所在的位置，请使用 get_user_location 工具来查找他们的位置。"""
```

3.使用框架自带的**装饰器**定义工具

```python
# 定义工具
@tool
def get_weather_for_location(city: str) -> str:
    """获取指定城市的天气。"""
    return f"{city}总是阳光明媚！"

@tool
def get_user_location(runtime: ToolRuntime[Context]) -> str:
    """根据用户 ID 获取用户信息。"""
    user_id = runtime.context.user_id
    return "Florida" if user_id == "1" else "SF"
```

4.配置模型(新版本中使用以下接口，而非直接创建模型实例)

```python
# 配置模型
model = init_chat_model(
    "anthropic:claude-sonnet-4-5",
    temperature=0
)
```

5.**设置记忆**

```python
# 设置记忆(demo的历史记忆是存在内存中)
checkpointer = InMemorySaver()
```

6.组装以上组件，构成智能体

```python
agent = create_agent(
    model=model,
    system_prompt=SYSTEM_PROMPT,
    tools=[get_user_location, get_weather_for_location],
    context_schema=Context,
    response_format=ResponseFormat,
    checkpointer=checkpointer
)
```

7.用**thread_id**做会话记忆隔离

```python
# 运行代理
# `thread_id` 是给定对话的唯一标识符。
config = {"configurable": {"thread_id": "1"}}

response = agent.invoke(
    {"messages": [{"role": "user", "content": "外面的天气怎么样？"}]},
    config=config,
    context=Context(user_id="1")
)

print(response['structured_response'])
# ResponseFormat(
#     punny_response="佛罗里达今天依然是'阳光灿烂'的一天！阳光正在播放'rey-dio'热门歌曲！我得说，这是进行'solar-bration'的完美天气！如果你希望下雨，恐怕这个想法已经'被冲走'了——预报仍然'清晰地'灿烂！",
#     weather_conditions="佛罗里达总是阳光明媚！"
# )

# 注意，我们可以使用相同的 `thread_id` 继续对话。
response = agent.invoke(
    {"messages": [{"role": "user", "content": "谢谢！"}]},
    config=config,
    context=Context(user_id="1")
)

print(response['structured_response'])
```

接下来在通过一个简单的对话式应用了解一下chain的概念。

- LLM: 语言模型是核心推理引擎。
- Prompt Templates: 提供语言模型的指令,即提示词模版。
- Output Parsers: 将LLM的原始响应转换为更易处理的格式，使得在下游使用输出变得容易。

1.LangChain中有两种类型的语言模型，称为:

- LLMs: 这是一个以**字符串作为输入并返回字符串**的语言模型
- ChatModels: 这是一个以**消息列表作为输入并返回消息**的语言模型，包括两个必须的组件：
  - `content`: 这是消息的内容。
  - `role`: 这是`ChatMessage`来自的实体的角色。

体会一下两者的差别

```python
from langchain_openai import OpenAI

# 初始化 LLM（字符串模型）
llm = OpenAI(
    model="gpt-3.5-turbo-instruct",
    temperature=0
)

# 直接传入字符串
response = llm.invoke("用一句话介绍人工智能")

print(response)
--------------------------------------------------
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage, SystemMessage

# 初始化 ChatModel
chat = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0
)

# 传入消息列表
response = chat.invoke([
    SystemMessage(content="你是一个严谨的科学家"),
    HumanMessage(content="用一句话介绍人工智能")
])

print(response.content)
```



LangChain提供了几个对象，用于方便地区分不同的角色:

- `HumanMessage`: 来自人类/用户的`ChatMessage`。
- `AIMessage`: 来自AI/助手的`ChatMessage`。
- `SystemMessage`: 来自系统的`ChatMessage`。
- `FunctionMessage`: 来自函数调用的`ChatMessage`。

总的来说，ChatModels支持角色定义，工具调用以及函数调用，使用更加广泛。

2.关于提示词模版

大多数LLM应用程序不会直接将用户输入传递到LLM中。通常，它们会将用户输入添加到一个更大的文本片段中，称为提示模板。框架中有类似于f-string功能的模版接口PromptTemplate。

```python
from langchain import PromptTemplate

# 无需传入变量的提示词
no_input_prompt = PromptTemplate(input_variables=[], template="Tell me a joke.")
no_input_prompt.format()
# -> "Tell me a joke."

# 一个输入变量的提示词
one_input_prompt = PromptTemplate(input_variables=["adjective"], template="Tell me a {adjective} joke.")
one_input_prompt.format(adjective="funny")
# -> "Tell me a funny joke."

# 支持多个输入变量的提示词
multiple_input_prompt = PromptTemplate(
    input_variables=["adjective", "content"], 
    template="Tell me a {adjective} joke about {content}."
)
multiple_input_prompt.format(adjective="funny", content="chickens")
# -> "Tell me a funny joke about chickens."
```

3.输出解析器

OutputParsers将LLM的原始输出转换为可以在下游使用的格式。输出解析器有几种主要类型，包括:

- 将LLM的文本转换为**结构化信息**（例如JSON）
- 将ChatMessage转换为字符串
- 将除消息之外的其他信息（如OpenAI函数调用）转换为字符串。

```python
from langchain.schema import BaseOutputParser

class CommaSeparatedListOutputParser(BaseOutputParser):
    """将LLM的字符串输出转换为逗号分隔的列表"""

    def parse(self, text: str):
        """Parse the output of an LLM call."""
        return text.strip().split(", ")

CommaSeparatedListOutputParser().parse("hi, bye")
# >> ['hi', 'bye']
```

4.chain组织链接各个模块

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts.chat import (
    ChatPromptTemplate,
    SystemMessagePromptTemplate,
    HumanMessagePromptTemplate,
)
from langchain.chains import LLMChain
from langchain.schema import BaseOutputParser

class CommaSeparatedListOutputParser(BaseOutputParser):
    """Parse the output of an LLM call to a comma-separated list."""


    def parse(self, text: str):
        """Parse the output of an LLM call."""
        return text.strip().split(", ")

template = """You are a helpful assistant who generates comma separated lists.
A user will pass in a category, and you should generate 5 objects in that category in a comma separated list.
ONLY return a comma separated list, and nothing more."""
system_message_prompt = SystemMessagePromptTemplate.from_template(template)
human_template = "{text}"
human_message_prompt = HumanMessagePromptTemplate.from_template(human_template)

chat_prompt = ChatPromptTemplate.from_messages([system_message_prompt, human_message_prompt])
chain = LLMChain(
    llm=ChatOpenAI(),
    prompt=chat_prompt,
    output_parser=CommaSeparatedListOutputParser()
)
chain.run("colors")
# >> ['red', 'blue', 'green', 'yellow', 'orange']
```

### 6.4 从0构建Agent框架

主流的框架功能十分强大，但本身学习周期长，学习曲线陡峭，因此通过结合教程，从0构建HelloAgent框架，理解整个智能体框架的技术细节。

#### 6.4.1 为什么构建自己的Agent框架

智能体目前是一个高速发展的领域，市面上存在各种智能体框架，每一种智能体框架有着不同的实现方式以及适用场景，但核心思想保持一致。

- 许多框架为了追求通用性，引入了大量抽象层和配置选项。以LangChain为例，其链式调用机制虽然灵活，但对初学者而言**学习曲线陡峭**，往往需要理解大量概念才能完成简单任务。
- **黑盒式**的实现过程不利于理解内部工作原理，许多框架将核心逻辑封装得过于严密，开发者难以理解Agent的内部工作机制，缺 乏深度定制能力。
- 依赖关系复杂，包的体积显得臃肿。

接下来，我们从每一个组件开始，深入理解和体会Agent的关于案例，同时进一步培养系统设计能力（包括模块化设计，接口抽象，错误处理等）。

本章参考的HelloAgent框架，是一个轻量级的智能体框架，同时兼具功能完整性以及学习友好性。

```bash
- 轻量级与学习友好，除了OpenAI的官方SDK和几个必要的基础库外，不引入任何重型依赖，遇到问题可以直接定位到框架本身
- 采用OpenAI标准接口，确保兼容性
- “万物皆工具”的抽象思想，在许多其他框架中需要独立学习的Memory（记忆）、RAG（检索增强生成）、RL（强化学习）、MCP（协议）等模块，在HelloAgents中都被统一抽象为一种“工具”。
```

#### 6.4.2 快速上手

一个简单的demo：

```python
from hello_agents import SimpleAgent,HelloAgentsLLM
from dotenv import load_dotenv
from hello_agents.tools import CalculatorTool

# 加载环境变量
load_dotenv()

# 创建LLM实例
llm = HelloAgentsLLM()

# 创建SimleAgent
agent = SimpleAgent(
    name="AI Assistant",
    llm=llm,
    system_prompt="你是一个专业的AI助手，能够回答用户的问题。",
)

response = agent.run("你好，请介绍一下自己👋")
print(response)

calculator = CalculatorTool()

response = agent.run("请计算1+5+9")
print(response)

# 查看历史对话
print(f"历史消息数：{len(agent.get_history())}")
```

#### 6.4.3 HelloAgentLLM扩展

我们对智能体的大脑进行封装和抽象，实现以下目标：

-  多提供商支持：实现对 OpenAI、ModelScope、智谱 AI 等多种主流 LLM 服务商的无缝切换，避免框 架与特定供应商绑定。
- 本地模型集成：引入 VLLM 和 Ollama 这两种高性能本地部署方案，满足数据隐私和成本控制的需求。
- 自动检测机制：建立一套自动识别机制，使框架能根据环境信息智能推断所使用的 LLM 服务类型，简 化用户的配置过程。

这里我们自定义一个继承自父类HelloAgentsLLM（教程LLM客户端）来捕获用户接入魔搭平台的需求：

```python
import os
from typing import Optional
from openai import OpenAI
from hello_agents import HelloAgentsLLM

class MyLLM(HelloAgentsLLM):
    """一个自定义的LLM客户端，继承自HelloAgentsLLM。"""
    def __init__(
        self,
        model:Optional[str] = None,
        api_key: Optional[str] = None,
        base_url: Optional[str] = None,
        provider: Optional[str] = "auto",
        **kwargs
    ):
        if provider == "modelscope":
            print("使用ModelScope提供的模型")
            self.provider = "modelscope"

            # 解析ModelScope凭证
            self.api_key = api_key or os.getenv("MODELSCOPE_API_KEY")
            self.base_url = base_url or os.getenv("LLM_BASE_URL")

            if not self.api_key:
                raise ValueError("密钥不存在，请在环境变量中设置MODELSCOPE_API_KEY❌️")

            self.model = model or os.getenv("LLM_MODEL_ID")
            self.temperature = kwargs.get("temperature", 0.7)
            self.max_tokens = kwargs.get("max_tokens", 1024)
            self.timeout = kwargs.get("timeout", 30)

            # 创建OpenAI客户端实例
            self._client = OpenAI(api_key=self.api_key, base_url=self.base_url, timeout=self.timeout)

        else:
            super().__init__(model=model, api_key=api_key, base_url=base_url, provider=provider, **kwargs)
```

为了保护数据隐私，我们应该支持本地模型调用，常见的如VLLM以及Ollama。它们都遵循了行业标准API，只需要在实例化LLM客户端时将其视为一个provider即可。

```python
llm_client = HelloAgentsLLM(
provider="vllm",
model="Qwen/Qwen1.5-0.5B-Chat", # 需与服务启动时指定的模型一致
base_url="http://localhost:8000/v1",
api_key="vllm" # 本地服务通常不需要真实API Key，可填任意非空字符串
)
```

自动检测机制，为了尽可能减少用户的配置负担并遵循“约定优于配置”的原则， HelloAgentsLLM内部设计了两个核心辅助方法：_auto_detect_provider以及_resolve_credentials,前者负责根据环境推断服务商，后者负责根据推理结果完成参数配置。

#### 6.4.4 框架接口实现

在上一节实现了智能体LLM客户端，解决了与LLM通信的问题，接下来学习配套接口和组件来处理数据流、管理配置、应对异常，包含三个核心文件：

- message.py：定义同一的消息格式
- config.py：定义配置管理方案，使框架易于调整和扩展
- agent.py：定义所有智能体的抽象基类，为后续实现不同类型的智能体提供统一范式

##### 6.4.4.1 Messages类

LLM作为智能体的大脑，通过对话与智能体交互，为了管理这些对话上下文，我们设计一个统一的Message类进行上下文工程。



