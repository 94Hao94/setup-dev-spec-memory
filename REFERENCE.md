# Reference: 开发规范记忆系统

## 当前记忆 ID 映射

| 章节 | Memory ID | 核心概念 |
|------|-----------|----------|
| 索引（v1.5） | mem_mr1xqfhx_3b5ad62cb298 | AI开发执行规范, 规范索引, 目录, v1.5 |
| 第一章 | mem_mr1woi1v_38b93f078a29 | AI行为准则, 操作确认, 透明度, 冲突处理 |
| 第二章 | mem_mr1wpggx_4cd709ad88f9 | 文档管理, 说明文档, 会话启动 |
| 第三章 | mem_mr03jko4_4cb193127ca4 | 需求分析, 任务规划, ToDoList |
| 第四章 | mem_mr03jzjp_8f24ce2abf96 | 编码规范, 代码标准, 日志规范 |
| 第五章 | mem_mr03koc1_1300ddafdc00 | 质量保证, 自审规范, 测试规范 |
| 第六章 | mem_mr03kpos_b960b7085831 | 版本控制, Git提交, 分支管理 |
| 第七章 | mem_mr03kq5v_6a0a15a3b2ca | 问题解决, 错误处理, 技术排查 |
| 第八章 | mem_mr1wtfjf_6c3fbb091afb | 汇报同步, 任务完成报告, 阶段汇总 |
| 多 agent 标注规范 | mem_mr1gdcrb_21cccf2ea9e6 | 来源前缀, agent 标识 |

## 规范文件信息

- 路径：`~/AI开发执行规范.md`
- 版本：v1.5
- 最后更新：2026-07-01
- 大小：~15KB

## CLAUDE.md 路径

- macOS/Linux: `~/.claude/CLAUDE.md`
- 作用：备用钩子，提醒调用 memory_recall

## 查询方式

### 获取完整索引

```
memory_recall query="AI开发执行规范 规范索引"
```

### 按章节查询

```
memory_recall query="操作确认 透明度" → 第一章
memory_recall query="文档管理 说明文档" → 第二章
memory_recall query="需求分析 ToDoList" → 第三章
memory_recall query="编码规范 日志" → 第四章
memory_recall query="质量保证 自审" → 第五章
memory_recall query="版本控制 Git" → 第六章
memory_recall query="问题解决 错误处理" → 第七章
memory_recall query="汇报同步 任务报告" → 第八章
```

### 按 Memory ID 精确查询

```
memory_verify id="mem_mr03jis9_8aecd75294f9"
```

## 故障排查

| 问题 | 解决方案 |
|------|----------|
| memory_recall 未返回规范 | 检查 agentmemory 服务状态 |
| 记忆 ID 失效 | 重新执行本 skill |
| 规范内容过期 | 更新本地文件后重新执行 skill |
| 重复记忆 | 使用 memory_governance_delete 清理 |

## 多 agent 来源标注规范

agentmemory 数据模型不存储 agentId，无法自动区分记忆来源 agent。
通过内容前缀实现来源标识。

### 前缀格式

| 前缀 | Agent |
|------|-------|
| `[Claude]` | Claude Code |
| `[Trae]` | Trae |
| `[Cursor]` | Cursor |
| `[Gemini]` | Gemini CLI |
| `[Copilot]` | GitHub Copilot / Copilot CLI |
| `[Codex]` | OpenAI Codex CLI |
| `[Kiro]` | Kiro |
| `[Warp]` | Warp AI |
| `[Zed]` | Zed AI |
| `[Cline]` | Cline |
| `[Continue]` | Continue |
| `[Qwen]` | Qwen / 通义灵码 |
| `[Droid]` | Droid |
| `[Antigravity]` | Antigravity |
| `[Other]` | 其他未列出的 agent |

### 示例

```
[Claude] 用户偏好使用 TypeScript strict 模式
[Trae] 项目已迁移到 pnpm，弃用 npm
```

### 背景

- 记忆系统本身不存储 `agentId` 字段
- `AGENT_ID` 环境变量仅用于 lease/signal 系统，不写入 observation
- 同一用户多 agent 并行时，需要人工标注来源

## 更新规范流程

1. 编辑 `~/AI开发执行规范.md`
2. 执行 `/setup-dev-spec-memory` 重新同步
3. 验证 memory_recall 返回最新内容
