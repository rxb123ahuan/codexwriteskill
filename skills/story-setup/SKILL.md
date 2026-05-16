---
name: story-setup
description: |
  网文写作项目初始化。为 Codex 创建小说项目目录、追踪文件、写作说明和本地规则文档；不部署 Claude Code hooks/agents。
  触发方式：/story-setup、「准备写书」「帮我搭一下环境」「配置写作项目」
  metadata:
  openclaw:
    source: https://github.com/worldwonderer/oh-story-claudecode
---

# story-setup：Codex 写作项目初始化
## Codex Compatibility

This skill was adapted from a Claude/OpenClaw skill set for Codex. Do not deploy `.claude/`, Claude hooks, Claude agents, or `CLAUDE.md`. For Codex, create plain project documentation and tracking files that Codex can read directly.

你是写作项目初始化器。为 Codex 可读的小说项目创建目录、追踪文件和写作说明。

**执行铁律：不覆盖用户已有配置，合并而非替换。**

---

## Phase 1：检测项目状态

1. 检查当前目录是否已部署过（存在 `.story-deployed`）
   - 如果已存在 → 向用户简短确认是否重新部署；若用户已明确要求继续，则直接增量更新
2. 检查是否有书名目录（包含 `追踪/` 子目录的目录，或用户自定义结构）
   - 有 → 识别为长篇项目，显示当前项目信息
   - 无 → 识别为新项目或短篇项目
3. 检查 `STORY.md`、`.codex-story/` 是否存在
   - 存在 → 后续按合并/保留用户内容处理
   - 不存在 → 后续创建
4. 检查 `.active-book` 文件是否存在
   - 存在 → 显示当前活跃书目
   - 不存在 → 跳过

## Phase 2：部署基础设施

确认部署位置后，依次执行：

### 2.1 部署 STORY.md
- 写入项目根目录 `STORY.md`，作为 Codex 阅读的项目说明
- 包含：项目名、目标平台、目录结构、写作规则、活跃书目、常用 skill 路由
- 如已存在，按「STORY.md 合并策略」处理

### 2.2 部署 Codex 规则文档
- 创建 `.codex-story/rules/`
- 写入或更新以下文件：
  - `story-format.md`：正文格式、章名、段落长度、文件命名
  - `story-outline.md`：大纲/细纲要求
  - `story-consistency.md`：设定、伏笔、时间线、信息权限一致性要求
  - `story-narrative.md`：网文节奏、钩子、爽点、去AI味规则

### 2.3 创建/补齐小说目录

如果是长篇项目，创建或补齐：
```
{书名}/设定/世界观
{书名}/设定/角色
{书名}/设定/势力
{书名}/大纲
{书名}/正文
{书名}/追踪
{书名}/对标
{书名}/参考资料
```

### 2.4 部署追踪模板
- 如 `{书名}/追踪/上下文.md` 不存在，按 `references/templates/上下文.md.tmpl` 初始化
- 如 `伏笔.md`、`时间线.md`、`信息权限.md` 不存在，创建空表骨架
- 不覆盖用户已有追踪内容

### 2.5 创建部署标记

- 创建 `.story-deployed` 文件（sentinel file）
- 写入以下字段：
  ```
  deployed_at: <date -u +"%Y-%m-%dT%H:%M:%SZ">
  mode: codex
  setup_skill_version: 2.0.0
  ```
- 此文件供写作 skill 检测部署状态，避免重复提示

## Phase 3：验证安装

1. 验证 `STORY.md` 存在
2. 验证 `.codex-story/rules/` 下规则文件存在
3. 验证书名目录和 `追踪/上下文.md`、`追踪/信息权限.md` 存在
4. 验证部署标记：
   - 检查 `.story-deployed` 是否存在且包含时间戳
5. 输出安装报告：
   - 列出所有已部署的文件
   - 列出需要注意的事项（如已有配置已合并）
   - 提示用户可以开始使用 `/story-long-write` 或 `/story-short-write`

---

## 模板占位符

| 占位符 | 替换规则 | 示例 |
|--------|----------|------|
| `{项目名}` | 用户项目名称或目录名 | 《剑来》、《暗卫》 |
| `{书名}` | 书名目录名（与目录一致） | 与 `{项目名}` 相同，或用户自定义 |
| `{目标平台}` | 目标发布平台 | 起点、番茄、晋江、知乎盐言 |
| `{作者名}` | 用户笔名或昵称 | 未指定时用「作者」 |

替换时去掉花括号。如果用户未指定项目名，用当前目录名。未指定的占位符保留原样不替换。

## STORY.md 合并策略

用户已有 STORY.md 时，按 section 合并：
1. 读取用户现有 STORY.md，按 `##` 标题切分为 section map
2. 生成 Codex 标准 section
3. 模板中的标准 section（Skill 路由表、文件结构、协作规则、Context Recovery、语言）**覆盖**用户同名 section
4. 用户独有的 section（自定义内容）**保留**不动
5. 未知冲突用简短问题向用户确认；如果用户已要求直接继续，优先保留用户原内容并追加 Codex 标准 section

## 重新部署

- `.story-deployed` 不存在 → 全新安装，Phase 2 全部执行
- `.story-deployed` 存在且 `mode: codex` → 提示已部署，用户要求继续时增量更新
- `.story-deployed` 存在但旧版本为 Claude 模式 → 保留旧 `.claude/` 内容，额外创建 Codex 的 `STORY.md` 和 `.codex-story/`

---

## 参考资料

| 文件 | 用途 |
|------|------|
| references/templates/rules/ | 可复用的写作规则文本（复制到 `.codex-story/rules/`） |
| references/templates/上下文.md.tmpl | 写作上下文模板 |
| references/templates/信息权限.md.tmpl | 秘密/金手指/隐藏身份知情边界模板 |

