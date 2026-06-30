# Reference: 开发规范记忆系统

## 当前记忆 ID 映射

| 章节 | Memory ID | 核心概念 |
|------|-----------|----------|
| 索引 | mem_mr03lnbz_c47a8a360bcf | AI开发执行规范, 规范索引, 目录 |
| 第一章 | mem_mr03jis9_8aecd75294f9 | AI行为准则, 操作确认, 透明度 |
| 第二章 | mem_mr03jk0i_323d86e11446 | 文档管理, 说明文档, 会话启动 |
| 第三章 | mem_mr03jko4_4cb193127ca4 | 需求分析, 任务规划, ToDoList |
| 第四章 | mem_mr03jzjp_8f24ce2abf96 | 编码规范, 代码标准, 日志规范 |
| 第五章 | mem_mr03koc1_1300ddafdc00 | 质量保证, 自审规范, 测试规范 |
| 第六章 | mem_mr03kpos_b960b7085831 | 版本控制, Git提交, 分支管理 |
| 第七章 | mem_mr03kq5v_6a0a15a3b2ca | 问题解决, 错误处理, 技术排查 |
| 第八章 | mem_mr03kqpg_e9061cd79b82 | 汇报同步, 任务完成报告, 阶段汇总 |

## 规范文件信息

- 路径：`~/AI开发执行规范.md`
- 版本：v1.1
- 最后更新：2026-06-28
- 大小：~14KB

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

## 更新规范流程

1. 编辑 `~/AI开发执行规范.md`
2. 执行 `/setup-dev-spec-memory` 重新同步
3. 验证 memory_recall 返回最新内容
