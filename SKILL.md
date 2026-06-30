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
Layer 2: CLAUDE.md ← 备用钩子
Layer 3: ~/AI开发执行规范.md ← 最后托底
```

## 执行步骤

### 0. 安装 agentmemory（如未安装）

```bash
# 检查是否已安装
which agentmemory || npm install -g agentmemory

# 启动服务
agentmemory start

# 连接当前智能体（claude-code / cursor / gemini-cli 等）
agentmemory connect claude-code

# 验证连接
agentmemory status
agentmemory doctor
```

参见 agentmemory-agents skill 获取完整安装指南。

### 1. 检查前置条件

- 规范文件存在：`~/AI开发执行规范.md`
- agentmemory 服务运行：`agentmemory status`

### 2. 清理旧记忆

```
memory_recall query="AI开发执行规范"
memory_governance_delete 旧记忆ID
```

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
Layer 2: 本文件 ← 备用钩子  
Layer 3: ~/AI开发执行规范.md ← 最后托底

## 禁止事项
- ❌ 正常情况下禁止读取本地规范文件
- ❌ 禁止跳过 memory_recall
```

### 6. 验证

```
memory_recall query="AI开发执行规范 规范索引"
```

## Anti-patterns

**WRONG**: 完整规范存为单个记忆  
**RIGHT**: 按章节拆分，按需召回

**WRONG**: CLAUDE.md 嵌入规范内容  
**RIGHT**: CLAUDE.md 只作为钩子

## Checklist

- [ ] agentmemory 已安装并运行
- [ ] agentmemory connect 已执行
- [ ] 规范文件存在
- [ ] 旧记忆已清理
- [ ] 8 章已分别存储
- [ ] 索引记忆已创建
- [ ] CLAUDE.md 已更新
- [ ] 验证通过

## See also

- agentmemory-agents: agentmemory 安装和连接指南
- agentmemory-config: 配置选项说明

## Reference

完整 Memory ID 映射和查询方式见 REFERENCE.md
