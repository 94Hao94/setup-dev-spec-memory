---
name: setup-dev-spec-memory
description: 将 AI 开发执行规范安装到 agentmemory 记忆系统，建立三层加载架构。Use when 首次配置开发环境、迁移到新机器、记忆系统重建、或规范更新后需要同步到记忆系统。
user-invocable: true
---

# 设置开发规范记忆系统

将 AI 开发执行规范存入 agentmemory，建立三层加载架构。

## 三层架构

```
Layer 1: agentmemory ← 主记忆源（规范分章存储）
Layer 2: Agent 配置钩子 ← Codex hooks / CLAUDE.md / 对应 agent 配置
Layer 3: ~/AI开发执行规范.md ← 最后托底
```

## 执行步骤

### 0. 安装并接入 agentmemory（如未安装）

先查阅官方文档：<https://github.com/rohitg00/agentmemory>。不要使用过时命令或硬编码某个 agent。

#### 0.1 启动记忆服务

```bash
# 如果已安装 agentmemory 命令，直接启动；否则使用 npx 启动
agentmemory
# 或
npx @agentmemory/agentmemory
```

服务应监听 `http://localhost:3111`。若系统已配置开机自启，只需验证：

```bash
agentmemory status
```

#### 0.2 接入当前 agent

按当前工具选择接入方式，不要固定使用 `claude-code`。

```bash
# 按当前 agent 选择一个 adapter
agentmemory connect codex
agentmemory connect claude-code
agentmemory connect cursor
agentmemory connect gemini-cli
```

Codex 推荐使用官方插件平台：

```bash
codex plugin marketplace add rohitg00/agentmemory
codex plugin add agentmemory@agentmemory

# Codex Desktop 需要额外写入全局 hooks workaround
agentmemory connect codex --with-hooks
```

安装 agentmemory 官方 skills，让 agent 知道何时调用记忆工具：

```bash
npx skills add rohitg00/agentmemory -y
```

最后验证：

```bash
agentmemory status
agentmemory doctor
```

参见 agentmemory-agents skill 获取完整 adapter 列表；参见 agentmemory 官方 README 获取最新安装方式。

### 1. 检查前置条件

- 规范文件存在：`~/AI开发执行规范.md`
- agentmemory 服务运行：`agentmemory status`

### 1.5 版本检查

比对本地文件版本与记忆系统中的版本：

```bash
# 读取本地文件版本
grep "规范版本" ~/AI开发执行规范.md

# 查询记忆中的版本
memory_recall query="AI开发执行规范 规范索引 版本"
```

- 如果版本一致 → 跳过后续步骤（无需重复同步）
- 如果版本不一致或记忆中无记录 → 继续执行后续步骤

### 2. 确认同步范围

列出将要更新的章节：

```bash
# 显示规范章节目录
grep "^## 第" ~/AI开发执行规范.md
```

新记忆会自动 supersede 旧记忆，无需手动删除。

### 3. 分章存储（8章）

| 章节 | concepts 标签 |
|------|---------------|
| 第一章：AI 行为准则 | AI行为准则,操作确认,透明度,边界意识 |
| 第二章：文档管理 | 文档管理,说明文档,会话启动 |
| 第三章：需求分析 | 需求分析,任务规划,ToDoList |
| 第四章：编码规范 | 编码规范,代码标准,日志规范 |
| 第五章：质量保证 | 质量保证,自审规范,测试规范 |
| 第六章：版本控制 | 版本控制,Git提交,分支管理 |
| 第七章：问题解决 | 问题解决,错误处理,技术排查 |
| 第八章：汇报同步 | 汇报同步,任务报告,阶段汇总 |

使用 `memory_save`，设置 `type: fact`，`files: ["~/AI开发执行规范.md"]`

**⚠️ 必须传 `project` 参数**（agentmemory 官方规范要求）：

```
memory_save \
  content="第一章：AI 行为准则..." \
  type="fact" \
  concepts="AI行为准则,操作确认,透明度,边界意识" \
  files="~/AI开发执行规范.md" \
  project="setup-dev-spec-memory"   # ← stable canonical project identifier
```

- `project` 必须是 **stable canonical identifier**（如 slug、UUID），不能用文件路径或临时名称
- 本项目固定使用 `project="setup-dev-spec-memory"`
- 参见 agentmemory 官方 MCP tool schema 中 `memory_save` 的 `project` 参数说明

### 4. 创建索引记忆

```
concepts: [AI开发执行规范, 规范索引, 最高优先级]
content: 版本信息 + 章节列表 + Memory ID 映射 + 三层架构说明
```

### 5. 更新 CLAUDE.md 为纯钩子

```markdown
# Claude Code 配置

## ⚠️ 启动指令
1. 调用 `memory_recall` 查询记忆

## 三层架构
Layer 1: agentmemory ← 主记忆源
Layer 2: 当前 agent 配置钩子 ← 备用钩子  
Layer 3: ~/AI开发执行规范.md ← 最后托底

## 禁止事项
- ❌ 正常情况下禁止读取本地规范文件
- ❌ 禁止跳过 memory_recall
```

### 6. 验证

```
memory_recall query="AI开发执行规范 规范索引"
```

### 7. 存储多 agent 来源标注规范

agentmemory 数据模型不存储 agentId，无法自动区分哪条记忆是哪个 agent 写入的。
通过内容前缀实现来源标识。

```
memory_save \
  content="【多 agent 来源标注规范】..." \
  type="preference" \
  project="setup-dev-spec-memory"
```

前缀格式：
- `[Claude]` — Claude Code
- `[Trae]` — Trae
- `[Cursor]` — Cursor
- `[Gemini]` — Gemini CLI
- `[Copilot]` — GitHub Copilot / Copilot CLI
- `[Codex]` — OpenAI Codex CLI
- `[Kiro]` — Kiro
- `[Warp]` — Warp AI
- `[Zed]` — Zed AI
- `[Cline]` — Cline
- `[Continue]` — Continue
- `[Qwen]` — Qwen / 通义灵码
- `[Droid]` — Droid
- `[Antigravity]` — Antigravity
- `[Other]` — 其他未列出的 agent

**Memory ID**: 见 REFERENCE.md

### 8. 创建快照

规范同步完成后，创建记忆快照以支持版本追踪和回滚：

```
memory_snapshot_create message="AI开发执行规范 v1.2 同步完成"
```

快照用途：
- 追踪规范的历史变化
- 记忆损坏时回滚到已知正确的版本
- diff 查看两次更新之间的变化

## Anti-patterns

**WRONG**: 完整规范存为单个记忆  
**RIGHT**: 按章节拆分，按需召回

**WRONG**: CLAUDE.md 嵌入规范内容  
**RIGHT**: CLAUDE.md 只作为钩子

**WRONG**: `memory_save` 不传 `project` 参数  
**RIGHT**: 必须传 `project`（stable canonical identifier），否则记忆无法按项目隔离

**WRONG**: 新 agent 接入时固定执行 `agentmemory connect claude-code`  
**RIGHT**: 按当前 agent 使用官方 adapter；Codex 使用插件平台并执行 `agentmemory connect codex --with-hooks`

**WRONG**: 闭门造车，分析 minified 源码或猜测 API 行为  
**RIGHT**: **开发/使用时必须查阅官方文档**（MCP tool schema、GitHub README、官方 wiki），不要逆向工程

## Checklist

- [ ] agentmemory 已安装并运行
- [ ] 当前 agent 的官方接入方式已执行（connect / plugin / hooks）
- [ ] agentmemory 官方 skills 已安装（`npx skills add rohitg00/agentmemory -y`）
- [ ] 规范文件存在
- [ ] 版本检查（本地 vs 记忆）
- [ ] 同步范围已确认
- [ ] 8 章已分别存储（**每条都带 `project="setup-dev-spec-memory"`**）
- [ ] 索引记忆已创建（含版本号 + project）
- [ ] 当前 agent 备用钩子已更新（如 CLAUDE.md / Codex hooks / 对应配置）
- [ ] 多 agent 来源标注规范已存储（带 project）
- [ ] 快照已创建
- [ ] 验证通过

## 开发习惯（必读）

> **使用任何工具/框架/服务时，必须先查阅官方文档**，不要分析 minified 源码或猜测 API 行为。
> 
> 例如：
> - 开发小程序 → 查微信小程序官方文档
> - 使用 agentmemory → 查 agentmemory GitHub README + MCP tool schema
> - 使用 React → 查 React 官方文档
> 
> 官方文档是最权威的来源，minified 源码容易误读，第三方博客可能过时。

## See also

- agentmemory-agents: agentmemory 安装和连接指南
- agentmemory-config: 配置选项说明

## Reference

完整 Memory ID 映射和查询方式见 REFERENCE.md
