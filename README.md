# setup-dev-spec-memory

将 AI 开发执行规范安装到 agentmemory 记忆系统，建立三层加载架构。

## 功能

- 将 AI 开发执行规范分章存入 agentmemory
- 建立三层加载架构（agentmemory → CLAUDE.md → 本地文件）
- 避免规范内容的冗余读取
- 支持故障降级和托底机制

## 三层架构

```
Layer 1: agentmemory ← 主记忆源（规范分章存储）
Layer 2: CLAUDE.md ← 备用钩子
Layer 3: ~/AI开发执行规范.md ← 最后托底
```

## 安装

### 方式一：复制到 Claude Code skills 目录

```bash
cp -r setup-dev-spec-memory ~/.claude/skills/
```

### 方式二：符号链接

```bash
ln -s $(pwd) ~/.claude/skills/setup-dev-spec-memory
```

## 使用

在 Claude Code 中执行：

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

1. 安装 agentmemory（如未安装）
2. 检查前置条件
3. 清理旧记忆
4. 分章存储规范（8章）
5. 创建索引记忆
6. 更新 CLAUDE.md 为纯钩子
7. 验证安装

## 相关文件

- `SKILL.md` - 主指令文件
- `REFERENCE.md` - Memory ID 映射和查询方式

## 依赖

- [agentmemory](https://github.com/agentmemory/agentmemory) - 记忆管理服务
- `~/AI开发执行规范.md` - AI 开发执行规范文件

## License

MIT
