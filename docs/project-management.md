# 个人知识库系统（漾的学习笔记）

> 🚀 基于 MkDocs + GitHub Pages 的自动化知识管理平台

---

## 一、项目背景

### 1.1 需求来源

作为即将入职金融科技行业的网络工程毕业生，我在备考 **H3CIE-RS+** 面试、准备 **期货从业资格证** 的日常学习中，产生了大量 XMind 思维导图笔记。最初面临几个痛点：

- ❌ 笔记散落在本地，无法随时随地从其他设备查看
- ❌ XMind 文件不方便分享，发给别人还要装软件
- ❌ 没有版本管理，改错了无法回溯
- ❌ 没有在线存档，换电脑或重装系统可能丢失

### 1.2 目标

> 搭建一个 **在线、免费、可维护、可扩展** 的个人知识库系统，实现：
>
> - ✅ 笔记在线浏览与原始文件下载
> - ✅ 自动构建与部署（写笔记 → 推代码 → 自动上线）
> - ✅ 版本控制（修改有记录，可回滚）
> - ✅ 分类导航，易于查阅
> - ✅ 零成本，持续维护

---

## 二、技术架构

### 2.1 整体流程

```
用户写笔记（Markdown / XMind）
        ↓
 git push 到 GitHub 仓库
        ↓
 GitHub Actions 自动触发
        ↓
 mkdocs build 构建静态 HTML
        ↓
 部署到 GitHub Pages
        ↓
 全球可访问 https://youngliang0311-ops.github.io/knowledge-base/
```

### 2.2 技术栈

| 组件 | 用途 | 版本/说明 |
|------|------|-----------|
| **MkDocs** | 静态站点生成器 | v1.6.1，将 Markdown 转成 HTML 网站 |
| **Material for MkDocs** | 主题框架 | v9.7.6，提供左侧导航、搜索、明暗切换等 |
| **GitHub** | 代码托管 | 免费公开仓库 |
| **GitHub Actions** | CI/CD 流水线 | push 触发自动构建部署 |
| **GitHub Pages** | 静态网站托管 | 免费 CDN 分发 |
| **Hermes Agent** | AI 助手 | 基于 Nous Research 的开源 AI Agent 框架 |
| **DeepSeek v4 Flash** | 底层大语言模型 | 负责需求分析、代码编写、架构设计、问题排查 |
| **pip（清华镜像）** | Python 包管理 | 在中国网络环境下使用 tuna 镜像加速 |
| **xmindparser** | XMind 解析工具 | 将 .xmind 思维导图转为 Markdown 格式 |
| **raw.githubusercontent.com** | 文件直链分发 | 解决 GitHub Pages 不识别 .xmind 文件的问题 |

### 2.3 运行环境

- **开发环境：** WSL2 (Ubuntu 24.04) on Windows 11
- **AI 助手运行环境：** WSL 下的 Python 虚拟环境
- **网络环境：** 中国网络（需使用国内镜像源/代理访问外网资源）

---

## 三、实现过程

### 3.1 第一阶段：站点搭建

- 配置 MkDocs + Material 主题，支持中文、明暗模式切换
- 编写 GitHub Actions 工作流，实现 push 后自动部署到 GitHub Pages
- 配置站点导航结构，支持多级目录

### 3.2 第二阶段：笔记归档

- 将 H3CIE-RS+ 面试笔记（3个 XMind 文件）转换为 Markdown 并归档
- 发现 GitHub Pages 不识别 `.xmind` 文件类型，改用 `raw.githubusercontent.com` 直链下载
- 保留原始 XMind 文件在站点中，支持直接下载

### 3.3 第三阶段：美化与迭代

- 导航栏从顶部改为**左侧可展开树状结构**（移除了 `navigation.tabs`）
- 色系改为**蓝色主题**，接近飞书 Wiki 风格
- 添加自定义 CSS，优化圆角、悬停效果、层级显示
- 顶部标题栏自动隐藏，提升阅读体验

### 3.4 遇到的问题与解决

| 问题 | 原因 | 解决 |
|------|------|------|
| pip 安装超时 | 中国网络访问 PyPI 慢 | 切换清华镜像 `-i https://pypi.tuna.tsinghua.edu.cn/simple` |
| git push 失败 | 中国网络访问 GitHub 不稳定 | 使用 token 嵌入 URL + 重试机制 |
| .xmind 文件在 GitHub Pages 返回 404 | Pages 不识别 .xmind MIME 类型 | 改用 `raw.githubusercontent.com` 直链下载 |
| .gitignore 误过滤 | `xmind/` 规则同时匹配了 `docs/xmind/` | 改为 `/xmind/` 仅匹配根目录 |
| 站点构建时 mkdocs-material 安装失败 | Venv 中网络超时 | 使用系统 pip + `--break-system-packages` 安装 |

---

## 四、目录结构

```
knowledge-base/
├── .github/workflows/deploy.yml   # GitHub Actions 自动部署配置
├── docs/
│   ├── index.md                    # 首页
│   ├── h3cie.md                    # H3CIE 笔记下载页
│   ├── stylesheets/extra.css       # 自定义样式
│   └── xmind/h3cie/               # 原始 XMind 文件（可下载）
│       ├── 第一章 二层技术.xmind
│       ├── 第2章 路由、控制.xmind
│       └── H3CIE面试题.xmind
├── xmind/                          # 本地 XMind 源文件（gitignore）
├── mkdocs.yml                      # 站点主配置
├── .gitignore
└── README.md
```

---

## 五、如何维护与扩展

### 5.1 添加新笔记

1. 将 XMind 文件放入本地 `xmind/` 目录下
2. 通知 Hermes Agent，它会：
   - 将 XMind 转换为 Markdown 或直接归档原始文件
   - 更新站点导航
   - 推送到 GitHub 自动部署

### 5.2 本地预览

```bash
cd /mnt/e/cherry/agent1/knowledge-base
python3 -m mkdocs serve
# 打开 http://localhost:8000
```

### 5.3 手动部署

直接 push 到 `main` 分支即可，GitHub Actions 会自动构建。

---

## 六、项目价值

### 6.1 对于当前岗位

- **文档规范意识**：金融行业对操作记录、变更管理、故障复盘文档要求严格，这个项目体现了规范化文档管理的习惯
- **自动化思维**：CI/CD 流程与运维自动化理念相通
- **版本控制能力**：Git 是网络 DevOps 的基础工具

### 6.2 对于长期发展

- 可扩展为团队知识库或技术文档平台
- 可接入更多笔记源（XMind、Markdown、思维导图截图等）
- 可添加更多分类（期货考证、Linux 运维、金融网络架构等）

---

## 七、关于本项目中的 AI 助手

本项目在构建过程中，由 **Hermes Agent** 作为 AI 编程助手全程参与。

- **框架：** Hermes Agent（由 Nous Research 开发的开源 AI Agent 框架）
- **模型：** DeepSeek v4 Flash（底层大语言模型）
- **能力：** 代码编写、Shell 命令执行、文件操作、Git 管理、Web 搜索、问题诊断

AI 助手负责了从需求分析、技术选型、代码实现、故障排查到文档归档的全流程工作，用户只需要提出需求、提供笔记源文件即可。

---

> 📅 项目创建时间：2026年6月  
> 📍 维护者：漾 / youngliang0311-ops  
> 🔗 站点地址：https://youngliang0311-ops.github.io/knowledge-base/
