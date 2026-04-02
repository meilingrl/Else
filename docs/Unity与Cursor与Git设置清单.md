# Unity + Cursor + Git 设置清单（一条龙）

适用于：**Unity 6**、**Cursor** 写 C#、**Git** 管理工程。按顺序做即可。

---

## 一、External Tools（你截图里的页面）

### 1. External Script Editor

- **可以**：保持 **Cursor (internal)** —— Unity 能识别时最省事。
- **若双击脚本打不开或版本错乱**：点 **Browse**，手动选 Scoop 下的可执行文件，例如：  
  `E:\Scoop\apps\cursor\current\Cursor.exe`（以你本机 `where cursor` 为准）。

### 2. External Script Editor Args（建议优化）

在 **`$(File)`** 基础上，建议改成 **带行号**（从 Unity Console 双击日志可跳到对应行；与 VS Code 系习惯一致）：

```text
-g "$(File)":$(Line):$(Column)
```

若 Cursor 某版本不认 `-g`，再改回 **`$(File)`** 即可。

### 3. 生成工程文件

在同一页底部点击 **Regenerate project files**（装包、换 Unity 版本、缺 IntelliSense 时各点一次）。

### 4. Embedded / 插件（按需）

- 未用 Rider 可关 **Rider** 相关；其余保持默认即可。

---

## 二、Project Settings（Git + 可读 diff，强烈建议）

### 2.1 Version Control（Unity 6 单独一页，不在 Editor 里）

菜单：**Edit → Project Settings → Version Control**（左侧与 **Editor** 并列的独立分类）。

| 项 | 推荐值 | 说明 |
|----|--------|------|
| **Mode** | **Visible Meta Files** | 每个资源有 `.meta`，Git 必跟踪，换机器不丢引用。 |

若在 **Editor** 页找不到该项，属正常；以 **Version Control** 页为准。也可在 `ProjectSettings/VersionControlSettings.asset` 中确认存在 `m_Mode: Visible Meta Files`。

### 2.2 Editor → Asset Serialization

菜单：**Edit → Project Settings → Editor**

| 项 | 推荐值 | 说明 |
|----|--------|------|
| **Asset Serialization → Mode** | **Force Text** | 场景/预制体文本化，`git diff` 有意义（合并仍要小心）。 |

---

## 三、Unity 与 Git（不在 Preferences 里，但必做）

- 工程已在仓库里时：**只提交** `Assets/`、`Packages/`、`ProjectSettings/`（及你主动加的脚本工具）；**不要提交** `Library/`、`Temp/`、`Logs/`、`UserSettings/`（本仓库 `.gitignore` 已忽略）。
- 大资源（音频长文件、高清贴图）多时再考虑 **Git LFS**；现阶段可不用。

---

## 四、Revision Control Diff / Merge（你截图里空白项）

**不影响**「用 Cursor 写代码 + Git 命令行/GitHub」。Unity 这项是给 **在 Unity 里点对比/合并** 时用的。

可选方案（二选一即可）：

**A. 暂不装第三方**  
保持 **Custom Tool** 但路径为空也行；需要看 diff 时用 **Cursor / GitHub Desktop / `git diff`**。

**B. 要 Unity 里也能点 diff**（可选）  
用 Scoop 装 **WinMerge** 或 **p4merge** 等，把 Unity 里 **Tool Path** 指到对应 `*.exe`，再按该工具文档填 **Two-way / Three-way** 命令行（各工具模板不同，装好后搜 “Unity custom merge tool”）。

---

## 五、开发体验优化（建议项）

### 5.1 进 Play 模式更快（Unity 6）

**Edit → Project Settings → Editor → Enter Play Mode Options**

- 可勾选 **Disable Domain Reload** / **Disable Scene Reload**（按项目需要；改静态初始化、单例时要谨慎，先白盒再开）。

### 5.2 Console

- **Console** 窗口右上角菜单：需要时可开 **Error Pause**，排逻辑 Bug 省事。

### 5.3 Cursor 侧

- 用 **打开文件夹** 打开仓库根目录 `E:\Dev\Projects\Else`（含 `Unity/My project`），不要只打开子目录，便于搜全局。
- 已装 **C# + Unity（Zlorn.vstuc）** 时：大改包或换 Unity 版本后，可 **Reload Window** 或重开 Cursor。

### 5.4 工程路径

- 长期建议把 **`My project`** 改名为无空格路径（如 `Else`），减少命令行与工具链麻烦；改名后 Unity 重指一次工程即可。

---

## 六、最小验收（5 分钟）

1. Unity：**Assets → Open C# Project**（或双击脚本）→ Cursor 能打开。  
2. Unity Console 里有一条带脚本的错误/日志 → **双击** → 能跳到大致行号（若用了 `-g` 参数）。  
3. `git status` 不出现 `Library/` 等被忽略目录的无关文件。

---

## 修订记录

| 日期 | 说明 |
|------|------|
| 2026-03-29 | 初版：External Tools、Editor、Git、Diff/Merge、体验项 |
