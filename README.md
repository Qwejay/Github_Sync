<div align="center">

# 🔄 Global Auto-Discovery Sync Hub

**全自动、零配置、多平台代码及 Release 镜像同步枢纽**

[![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/Qwejay/Github_Sync/sync.yml?branch=main&logo=github&label=Sync%20Status)](https://github.com/Qwejay/Github_Sync/actions)
[![License: GPL v3](https://img.shields.io/badge/License-GPL_v3-blue.svg)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/Qwejay/Github_Sync?color=blue)](https://github.com/Qwejay/Github_Sync/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/Qwejay/Github_Sync?color=brightgreen)](https://github.com/Qwejay/Github_Sync/network/members)

[✨ 核心特性](#-核心特性) • [🧱 架构图示](#-工作原理与架构) • [🚀 快速开始](#-快速开始三步配置) • [⚙️ 高级配置](#%EF%B8%8F-高级自定义配置) • [❓ 常见问题](#-常见问题)

</div>

---

> 💡 **项目说明**：本项目旨在解决开发者在多个国内/国际代码托管平台（Gitee、GitLab、GitCode 等）之间手动同步仓库的痛点。只需 **Fork 本仓库** 并配置对应的令牌，系统就会自动发现你 GitHub 上的所有公开仓库并全量镜像推送！

---

## 🧱 工作原理与架构

```text
               ┌───────────────────────────────────────────────┐
               │    你的 GitHub 账号 (Auto Public Discovery)    │
               └───────────────────────┬───────────────────────┘
                                       │
                                       ▼
                       ┌──────────────────────────────┐
                       │  Sync Hub (GitHub Actions)   │
                       └───────────────┬──────────────┘
                                       │ (并行 Matrix 同步)
        ┌──────────────────────────────┼──────────────────────────────┐
        ▼                              ▼                              ▼
┌──────────────┐               ┌──────────────┐               ┌──────────────┐
│  Gitee 码云  │               │    GitLab    │               │   GitCode    │
│ 代码+Release │               │   全量代码    │               │   全量代码   │
└──────────────┘               └──────────────┘               └──────────────┘
```

1. **自动诊断**：调用 API 获取使用者在目标平台的真正用户名，并检查已配置的 Token。
2. **仓库扫描**：自动拉取你名下所有的**非 Fork 公开仓库**，过滤本同步 Hub 仓库本身。
3. **自动建库**：如果目标平台不存在同名仓库，自动调用 API 创建公共仓库。
4. **镜像推送**：使用 `git push --mirror` 完整同步所有分支、Commit 历史及 Git Tags，并同步 Releases 至 Gitee。

---

## ✨ 核心特性

- 🚀 **零代码修改 / Fork 即用**：通过 `${{ github.repository_owner }}` 自动感知使用者账号，无需硬编码任何个人信息。
- 🛡️ **全平台动态诊断**：无需填写目标平台用户名，系统通过 API 智能识别底层 Token 对应的真实用户。
- 🔌 **模块化设计 (按需启用)**：支持 Gitee、GitLab、GitCode。**没填哪个平台的 Token 就自动跳过哪个平台**，绝不报错中断。
- 📦 **全自动远程建库**：目标平台缺少对应仓库时，API 会自动帮你新建，无需提前去各个平台逐个点按建库。
- 🔄 **镜像级同步**：镜像推送所有 Branches、Tags 和提交记录；支持将 GitHub Releases 资产无缝迁移至 Gitee。
- 💰 **100% 免费无扣费**：本仓库保持 **Public** 状态，充分利用 GitHub 对公开仓库 Actions **无上限免费分钟数** 的官方福利！

---

## 🚀 快速开始（三步配置）

### 第一步：Fork 本仓库
点击本项目右上角的 **`Fork`** 按钮，将项目 Fork 到你的 GitHub 账号下。

> ⚠️ **重要提示**：请确保 Fork 后的仓库保持 **Public（公开）** 状态。若改成 Private，GitHub 会扣除你的每月免费 Actions 分钟额度。

---

### 第二步：获取目标平台的 Access Token

根据你需要同步的目标平台，申请对应的个人访问令牌（Personal Access Token）：

| 平台 | Token 申请地址 | 权限要求 (Scopes) |
| :--- | :--- | :--- |
| **Gitee (码云)** | [Gitee 私人令牌设置](https://gitee.com/profile/personal_access_tokens) | 勾选 `projects` (项目权限) |
| **GitLab** | [GitLab Personal Access Tokens](https://gitlab.com/-/user_settings/personal_access_tokens) | 勾选 `api`，Role 选择 `Developer` 或 `Owner` |
| **GitCode** | [GitCode 访问令牌](https://gitcode.com/setting/tokens) | 勾选 `repo` (仓库读写权限) |

---

### 第三步：配置 GitHub Secrets

进入你 **Fork 后的仓库设置页面**：
1. 点击顶部 **`Settings`** -> 左侧 **`Secrets and variables`** -> **`Actions`**。
2. 点击 **`New repository secret`**，根据需求添加以下 Key-Value（不需要同步的平台留空不填即可）：

| Secret Key | 是否必填 | 说明 |
| :--- | :--- | :--- |
| `GITEE_TOKEN` | 选填 | 填入刚才获取的 Gitee 私人令牌 |
| `GITLAB_TOKEN` | 选填 | 填入刚才获取的 GitLab Access Token |
| `GITCODE_TOKEN` | 选填 | 填入刚才获取的 GitCode Access Token |

> 🔒 **安全承诺**：你的 Token 加密存储在 GitHub 官方 Secrets 中，仅在运行构建时由 GitHub 内部读取，**任何人都无法通过公开仓库查看你的 Token**。

---

## ⚡ 手动运行与测试

配置完成后，推荐手动测试一次：
1. 在你的仓库顶部点击 **`Actions`** 选项卡。
2. 在左侧菜单点击 **`Universal Multi-Platform Code Sync Hub`** 工作流。
3. 如果界面提示 *"Workflows are disabled"*，点击大红按钮 **`I understand my workflows, enable them`**。
4. 点击右侧的 **`Run workflow`** -> **`Run workflow`** 即可触发同步！

---

## ⚙️ 高级自定义配置

如果你对工作流有更个性化的需求，可以编辑 `.github/workflows/sync.yml` 文件：

### 1. 修改自动同步频率
默认配置为每天北京时间凌晨 02:00 运行一次：
```yaml
on:
  schedule:
    - cron: '0 18 * * *' # UTC 时间 18:00，即北京时间 +8 小时 = 02:00
```
如果你想改为**每周日凌晨 02:00** 运行一次：
```yaml
  schedule:
    - cron: '0 18 * * 0'
```

### 2. 过滤不想同步的特定仓库
默认同步你名下的**所有非 Fork 公开仓库**。如果你想排除某个特定的仓库（例如 `test-repo`），可以修改工作流中的 `jq` 过滤语句：
```bash
# 修改前
|[.[] | select(.fork == false and .name != $SELF) | .name]

# 修改后 (排除 test-repo 和 demo-repo)
|[.[] | select(.fork == false and .name != $SELF and .name != "test-repo" and .name != "demo-repo") | .name]
```

---

## 📂 项目结构

```text
.
├── .github/
│   └── workflows/
│       └── sync.yml      # 核心逻辑：多平台动态发现与镜像同步工作流
├── LICENSE               # GNU General Public License v3.0 (GPLv3)
└── README.md             # 项目说明文档
```

---

## ❓ 常见问题 (FAQ)

<details>
<summary><b>Q: 仓库保持 Public 状态，我的 Token 会泄漏吗？</b></summary>
<b>绝对安全！</b> GitHub Secrets 使用了基于 Libsodium 的非对称加密。即使仓库是公开的，Secrets 在日志输出时也会被系统强制替换为 <code>***</code> 遮蔽，他人无法获取。
</details>

<details>
<summary><b>Q: 如果某个仓库提交很大，会导致工作流超时吗？</b></summary>
GitHub Actions 单个 Job 允许最长运行 6 小时。本项目使用了并行矩阵（Matrix），每个仓库独立并行运行，单个大仓库的推送不会阻塞其他仓库。
</details>

<details>
<summary><b>Q: 为什么某些 Release 文件没有成功同步到 Gitee？</b></summary>
请确保你的 <code>GITEE_TOKEN</code> 勾选了足够的权限。Gitee 对单个 Release 附件大小有限制，如果单文件超过 Gitee 平台限制（如 50MB/100MB），可能会触发 Gitee API 上传失败，但这不会影响 Git 代码仓库的同步。
</details>

---

## 🤝 贡献与反馈

欢迎提交 [Issues](https://github.com/Qwejay/Github_Sync/issues) 反馈 Bug 或提出了不起的新想法！
如果你觉得这个项目帮到了你，不妨点一个 ⭐️ **Star** 鼓励一下作者！

---

## 📄 许可证

本项目基于 [GNU General Public License v3.0 (GPLv3)](LICENSE) 开源。
````
