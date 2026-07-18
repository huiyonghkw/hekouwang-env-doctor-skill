# 开发环境体检器 · env-doctor

> 会勇禾口王「体检器家族」第三把 —— 前两把体检文件，这把体检机器。

<p align="center">
  <img src="demo/env-doctor.gif?v=2" alt="env-doctor 演示：把磁盘占用分成 残留/缓存/数据 三组，重点标出「你已经不用的工具」" width="100%">
</p>

<p align="center">
  <sub>▲ Claude Code 里的真机实录 · 数字为作者本机实测</sub>
</p>

### 想清理时：交互式选择器

<p align="center">
  <img src="demo/selector.gif?v=2" alt="清理选择器：空格勾选、数据类锁死、实时合计、确认后才执行" width="100%">
</p>

<p align="center">
  <sub>▲ 真机实录：<code>bash scripts/clean.sh --dry-run</code> 先看不执行，确认页逐条列出命令与代价</sub>
</p>

## 它解决什么

Mac 用久了空间告急，你翻相册删视频，其实真正的大户是**开发工具在你看不见的地方囤的东西**：下载缓存、旧版本、构建产物、模型权重。而其中最该清、也最难发现的一类是——**你已经换掉、但从没卸干净的旧工具**。

市面清理工具回答「哪个目录大」，本器回答「**这个工具你还在用吗**」。

## 差异化：四信号身份判定

对每个版本管理器交叉验证四个信号——shell 配置是否加载、命令是否在 PATH、运行时实际由谁提供、目录多久没动——判定它是**活跃**还是**残留**。

实测样例：

```
nvm    9.8 GB  未加载  不在PATH  node由fnm提供  最后改动 2025-03  → 残留 ✅可清
fnm    1.7 GB  已加载  在PATH    node由它提供   最后改动 2026-05  → 活跃 ⛔别动
```

体积扫描器发现不了这件事，因为这是推理不是测量。

## 三条铁律

1. **删除只能来自你逐项勾选** —— 默认全不选、数据类锁死勾不上、只跑白名单里写死的命令、输入 `yes` 才动手、随时可 `--dry-run` 先看不执行。**模型自己不会拼一条 `rm` 跑掉**。
2. **扫描器只读** —— `scan.sh` 不含任何写操作；删除只发生在 `clean.sh` 里。
3. **不确定就说不确定** —— 信号矛盾时如实标注，不猜。

> 顺带一提：**这个工具不替你改 `.zshrc`**。删完版本管理器目录，它只提示你去清理 shell 里的初始化行——自动改配置比删目录更危险。

## 用法

```
磁盘满了，帮我看看什么占了空间
```

或直接跑脚本：

```bash
bash scripts/scan.sh          # 体检（只读，全量）
bash scripts/scan.sh --quick  # 体检（只读，快扫）
bash scripts/clean.sh --dry-run   # 清理选择器 · 只演示不执行
bash scripts/clean.sh             # 清理选择器 · 勾选后执行
```

体检器会把结果分成 🟠残留 / 🟡缓存 / 🔴数据 三组，**逐组问你**，确认后才给命令。

## 结构

```
SKILL.md              # 路由 + 铁律
scripts/
  scan.sh             # 只读扫描器
  clean.sh            # 清理选择器（唯一允许删除处，白名单驱动）
references/
  rules.md            # 残留特征规则库（工具→目录→判活法→官方命令→代价）
  report.md           # 报告格式与对话流程
  safety.md           # 安全铁律与自检清单
demo/
  env-doctor.gif      # Claude Code 里出体检报告 · 真机实录
  selector.gif        # 清理选择器 · 真机实录
```

## 覆盖范围

版本管理器（nvm/fnm/volta/pyenv/rbenv/rvm/asdf/mise/sdkman/jenv/conda）· 包管理器缓存（npm/yarn/pnpm/bun/pip/uv/cargo/go/maven/gradle）· 容器与模拟器（Docker/CoreSimulator/Xcode）· AI 模型缓存（huggingface/ollama/torch）

**不覆盖**：系统缓存、照片、邮件、聊天软件——那些交给系统「存储空间」。

## 许可

MIT · © 会勇禾口王的AI笔记 @huiyonghkw
