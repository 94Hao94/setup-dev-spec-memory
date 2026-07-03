# setup-dev-spec-memory

将 AI 开发执行规范安装到 agentmemory 记忆系统，建立三层加载架构。

## 功能

- 将 AI 开发执行规范分章存入 agentmemory
- 建立三层加载架构（agentmemory → agent 配置钩子 → 本地文件）
- 避免规范内容的冗余读取
- 支持故障降级和托底机制
- 多 agent 来源标注规范（区分 Claude/Trae/Cursor 等写入的记忆）

## 三层架构

```
Layer 1: agentmemory ← 主记忆源（规范分章存储）
Layer 2: Agent 配置钩子 ← Codex hooks / CLAUDE.md / 对应 agent 配置
Layer 3: ~/AI开发执行规范.md ← 最后托底
```

## 安装本 skill

将本目录复制或链接到目标 agent 支持的 skills 目录。不同 agent 的目录不同；不要只安装到 Claude Code。

### Claude Code

```bash
cp -r setup-dev-spec-memory ~/.claude/skills/
```

或：

```bash
ln -s $(pwd) ~/.claude/skills/setup-dev-spec-memory
```

### Codex

将本 skill 放入 Codex 可发现的 skills 目录，或在当前 workspace 中使用。若需要安装 agentmemory 官方 skills，使用：

```bash
npx skills add rohitg00/agentmemory -y
```

## 接入 agentmemory 到新 agent

先查阅官方文档：<https://github.com/rohitg00/agentmemory>。

### 1. 启动记忆服务

```bash
agentmemory
# 或
npx @agentmemory/agentmemory
```

如果系统已配置开机自启，只需验证：

```bash
agentmemory status
```

### 2. 按 agent 类型接入

通用 MCP 接入：

```bash
# 按当前 agent 选择一个 adapter
agentmemory connect codex
agentmemory connect claude-code
agentmemory connect cursor
agentmemory connect gemini-cli
```

常见 adapter：`claude-code`、`codex`、`cursor`、`gemini-cli`、`copilot-cli`、`qwen`、`warp`、`zed`。

Codex 推荐完整插件方式：

```bash
codex plugin marketplace add rohitg00/agentmemory
codex plugin add agentmemory@agentmemory
agentmemory connect codex --with-hooks
```

再安装官方 skills：

```bash
npx skills add rohitg00/agentmemory -y
```

### 3. 验证

```bash
agentmemory status
agentmemory doctor
```

## 使用

在支持 invocable skills 的 agent 中执行：

```
/setup-dev-spec-memory
```

### 触发场景

- 首次配置开发环境
- 迁移到新机器
- 记忆系统重建
- 规范更新后同步

## 前置条件

- `~/AI开发执行规范.md` 文件存在
- agentmemory 服务已安装并运行

## 执行步骤

1. 安装并接入 agentmemory（如未安装）
2. 检查前置条件
3. 检查本地规范版本与记忆版本
4. 分章存储规范（8章）
5. 创建索引记忆
6. 更新当前 agent 的备用钩子
7. 存储多 agent 来源标注规范
8. 创建快照并验证安装

## 相关文件

- `SKILL.md` - 主指令文件
- `REFERENCE.md` - Memory ID 映射和查询方式

## 依赖

- [agentmemory](https://github.com/rohitg00/agentmemory) - 记忆管理服务
- `~/AI开发执行规范.md` - AI 开发执行规范文件

## License

MIT
