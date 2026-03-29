# 《Else》开发环境配置（Scoop 优先）

本文约定：**能用 Scoop 安装的用 Scoop 管理版本与路径**；引擎模块、大型 IDE 工作负载等 **Scoop 无法完整覆盖的，用手动安装 + 文中固定步骤**，保证可复现、可迁移。

---

## 1. 环境分层（建议记住的三层）

| 层级 | 内容 | 推荐管理方式 |
|------|------|----------------|
| 工具链 | Git、编辑器、Hub、SDK | Scoop 为主 |
| 引擎 | Unity Editor 具体版本 | **仅通过 Unity Hub 安装**（Scoop 只装 Hub） |
| 项目 | 仓库、`.gitignore`、Cursor 规则 | Git + 文档 |

---

## 2. Scoop 自身（若尚未安装）

官方安装说明：<https://github.com/ScoopInstaller/Install#readme>

**PowerShell（当前用户，推荐）**：

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

**建议：固定 Scoop 根目录（便于备份与多盘管理）**  
例如你当前使用 `E:\Scoop`，可在安装前设置：

```powershell
$env:SCOOP = 'E:\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')
```

然后重新打开终端，再执行官方 `install.ps1`。

**全局程序目录（可选）**：

```powershell
$env:SCOOP_GLOBAL = 'E:\ScoopGlobal'
[Environment]::SetEnvironmentVariable('SCOOP_GLOBAL', $env:SCOOP_GLOBAL, 'Machine')  # 需管理员；或仅用 User + 不用 global install
```

---

## 3. Scoop 桶（Bucket）—《Else》建议至少启用

```powershell
scoop bucket add main       # 默认已有可跳过
scoop bucket add extras
scoop bucket add nonportable
scoop bucket add versions   # 可选：用于 dotnet-sdk-lts 等固定大版本
```

说明：

- **`nonportable`**：Unity Hub 的安装脚本需要管理员权限，包名一般为 **`unity-hub-np`**。
- **`extras`**：`cursor`、`vscode` 等常用工具。

---

## 4. 用 Scoop 安装（推荐顺序）

在 **PowerShell** 中执行（可按需删减）。

### 4.1 基础工具

```powershell
scoop install git
scoop install 7zip          # Scoop 解压依赖，建议装
```

可选（大仓库 / LFS 资源）：

```powershell
scoop install git-lfs
git lfs install
```

### 4.2 编辑器 / IDE

**方案 A：Cursor（你已在用，可纳入 Scoop 管理）**

```powershell
scoop install cursor
```

**方案 B：Visual Studio Code（轻量，需自行配 C# / Unity 插件）**

```powershell
scoop install vscode
```

安装后请在 VS Code 中安装：**C# Dev Kit**（或 C#）+ **Unity** 相关扩展，并按 Unity 官方说明关联 Editor。

**方案 C：JetBrains Rider（Scoop 可装，Unity C# 体验好，商业许可需自行解决）**

```powershell
scoop install rider
```

### 4.3 .NET SDK（可选，与 Unity 内置不冲突）

用于命令行工具、部分测试或全局 `dotnet` 命令：

```powershell
scoop install dotnet-sdk
# 或固定大版本（需 versions 桶）例如：
# scoop install versions/dotnet-sdk-lts
```

验证：

```powershell
dotnet --info
```

### 4.4 Unity Hub（Scoop：`unity-hub-np`，需管理员）

```powershell
scoop install unity-hub-np
```

注意：

- 安装过程会 **提权（UAC）**，属正常行为。
- **Unity Editor 本体** 必须在 Hub 里选择版本安装；**不要用 Scoop 装 Editor**。

---

## 5. 无法仅靠 Scoop 完成的部分（手动教程）

### 5.1 Unity Editor + 模块

1. 打开 **Unity Hub**（由 `unity-hub-np` 或手动安装得到）。
2. **Installs** → **Install Editor**。
3. 选择与团队文档一致的版本线（例如 **2022.3 LTS** 或 **Unity 6**，二选一为主版本，不要混用多个主版本）。
4. 勾选模块建议：
   - **Microsoft Visual Studio Community**（含 Unity 工作负载），**或** 你已安装 **Rider** / **VS Code** 则按需勾选。
   - **Windows Build Support**（本机开发必备）。
   - Android / iOS 等 **按需延后**，减少首次体积。

官方文档：<https://docs.unity3d.com/Manual/GettingStartedInstallingHub.html>

### 5.2 Visual Studio 2022（完整「游戏开发」工作负载）

若希望 **与 Unity 官方一键勾选** 的最省事路线：

1. 打开 <https://visualstudio.microsoft.com/downloads/>  
2. 下载 **Visual Studio Community 2022**。  
3. 安装器中选择工作负载：**使用 Unity 的游戏开发**（或 `.NET 桌面开发` + Unity 文档要求组件）。  
4. 在 Unity Editor **Preferences → External Tools** 中指定外部脚本编辑器为 Visual Studio。

说明：完整 VS 一般 **不通过 Scoop 装全量工作负载**；可用 Scoop 管理 **VS Code / Rider / Cursor**，VS 走官方安装器更稳。

### 5.3 美术 / 音频（分期即可）

| 软件 | Scoop | 说明 |
|------|--------|------|
| Aseprite | 可能有第三方 bucket，**不强制** | 官网 <https://www.aseprite.org/> 或 Steam 购买；也可用免费替代先占位。 |
| FMOD / Wwise | 一般 **否** | 官网下载安装；MVP 可先用 Unity 自带 Audio。 |

---

## 6. Git 最小配置（规范）

```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Windows 换行（推荐二选一，团队统一即可）：

```powershell
git config --global core.autocrlf true
```

---

## 7. 与《Else》仓库的衔接

- 技术约定见：`docs/05-技术架构与代码约定.md`
- Unity 工程创建后，应添加 **Unity 专用 `.gitignore`**（若尚未加入仓库），避免提交 `Library/`、`Temp/` 等。

---

## 8. 验证清单（装完一轮后自查）

```powershell
scoop --version
git --version
dotnet --info          # 若已装 SDK
```

- Unity Hub 能打开，且已安装 **一个** 主版本 Editor。  
- 任选：**Cursor / Rider / VS / VS Code** 其一，可在 Unity **External Tools** 中指向。  
- 在 Unity 中新建空项目能编译通过（`Ctrl+R` 或保存脚本无红错）。

---

## 9. Git 仓库（本机已初始化）

仓库根目录：`E:\Dev\Projects\Else`（含 `docs/`、`.cursor/`、`Unity/My project/`）。

- 已执行 `git init`，默认分支为 **`main`**，并有一次初始提交（含文档、Cursor 规则、Unity 工程应入库部分）。
- 根目录 **`.gitignore`**：忽略任意子工程下的 **`Library/`、`Temp/`、`Logs/`、`UserSettings/`、`Obj/`** 等；忽略 Unity 再生成的 **`*.sln` / `*.csproj`**（由 Editor 重新生成）。
- 根目录 **`.gitattributes`**：声明常见二进制类型；文本使用 `text=auto`（与 Windows 上 `core.autocrlf` 配合即可）。

**后续你本地可补**（未替你执行，因涉及账号）：

```powershell
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
git config --global init.defaultBranch main
```

远程托管（GitHub / Gitee 等）在平台创建空仓库后：

```powershell
cd E:\Dev\Projects\Else
git remote add origin <远程仓库 URL>
git push -u origin main
```

---

## 10. 常用 Scoop 维护命令

```powershell
scoop update scoop
scoop update *
scoop cleanup *
scoop checkup
```

---

## 11. 修订记录

| 日期 | 说明 |
|------|------|
| 2026-03-28 | 初版：Scoop 优先 + Unity Hub / Editor / VS 手动分界 |
| 2026-03-29 | 补充：Git 初始化说明与 `.gitignore` / `.gitattributes` |
