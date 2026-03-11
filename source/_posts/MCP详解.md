---
title: MCP详解
date: 2026-03-11 18:29:11
tags: [AI, MCP]
categories: AI
---

{% asset_img MCP.png MCP（Model Context Protocol，模型上下文协议）%}

<!--more-->


## 一、MCP 是什么

### 1. 定义

**MCP（Model Context Protocol，模型上下文协议）** 是 Anthropic 于 2024 年 11 月发布的一项**开放标准协议**。它定义了 AI 应用（如 Cursor、Claude Desktop、Windsurf 等）与外部数据源/工具之间的标准化通信方式。

一句话概括：**MCP 是 AI 世界的 USB-C 接口**。

### 2. MCP 解决了什么问题

在 MCP 出现之前，每个 AI 应用想要访问外部数据（数据库、文件系统、API 等），都需要单独写集成代码：

```
没有 MCP 的世界：

Cursor   ──自定义代码──→ PostgreSQL
Cursor   ──自定义代码──→ GitHub API
Cursor   ──自定义代码──→ Slack API
Claude   ──另一套代码──→ PostgreSQL
Claude   ──另一套代码──→ GitHub API
Windsurf ──又一套代码──→ PostgreSQL

= M 个应用 × N 个数据源 = M×N 种集成
```

MCP 出现后：

```
有 MCP 的世界：

Cursor   ─┐                  ┌─→ PostgreSQL MCP Server
Claude   ─┤── MCP 协议 ──→  ├─→ GitHub MCP Server
Windsurf ─┘                  └─→ Slack MCP Server

= M + N 种集成（每个应用实现一次 MCP Client，每个数据源实现一次 MCP Server）
```

### 3. 核心架构

MCP 采用**客户端-服务器架构**：

```
┌──────────────────────────────────────────────────────┐
│                    宿主应用 (Host)                     │
│              如 Cursor IDE、Claude Desktop            │
│                                                       │
│  ┌─────────────────────────────────────────────────┐  │
│  │               MCP Client（客户端）               │  │
│  │         内嵌在宿主应用中，管理与 Server 的连接     │  │
│  └────────┬──────────────┬──────────────┬──────────┘  │
└───────────┼──────────────┼──────────────┼─────────────┘
            │              │              │
        MCP 协议       MCP 协议       MCP 协议
      (JSON-RPC 2.0) (JSON-RPC 2.0) (JSON-RPC 2.0)
            │              │              │
            ▼              ▼              ▼
     ┌────────────┐ ┌────────────┐ ┌────────────┐
     │ MCP Server │ │ MCP Server │ │ MCP Server │
     │ PostgreSQL │ │   GitHub   │ │ 文件系统    │
     └──────┬─────┘ └─────┬──────┘ └─────┬──────┘
            │              │              │
            ▼              ▼              ▼
       PostgreSQL     GitHub API      本地文件
```

### 4. MCP 的三大核心能力

| 能力 | 类比 | 说明 |
|------|------|------|
| **Tools（工具）** | POST 请求 | 可执行的操作。AI 可以调用，会产生副作用。如：执行 SQL 查询、创建 GitHub Issue、发送消息 |
| **Resources（资源）** | GET 请求 | 只读数据。AI 可以读取但不会修改。如：数据库表结构、配置文件内容、API 文档 |
| **Prompts（提示模板）** | 函数模板 | 预定义的 Prompt 模板。如：代码审查模板、SQL 生成模板 |

### 5. MCP 的通信方式

MCP 使用 **JSON-RPC 2.0** 协议进行通信，支持两种传输方式：

**stdio（标准输入输出）**：MCP Server 作为本地子进程运行，Client 通过 stdin/stdout 与其通信。适合本地工具（如 Cursor 中使用的方式）。

```
Cursor (Client) ──stdin/stdout──→ MCP Server 进程
```

**HTTP + SSE（Server-Sent Events）**：MCP Server 作为独立 HTTP 服务运行，Client 通过网络与其通信。适合远程服务、多用户共享场景。

```
Client ──HTTP POST──→ MCP Server (远程)
Client ←──SSE────── MCP Server (远程)
```

### 6. MCP Server 的工作原理

一个 MCP Server 本质上就是一个程序，它：

1. **启动后声明自己提供了哪些 Tools、Resources、Prompts**（通过 `initialize` 握手）
2. **等待 Client 的请求**（通过 JSON-RPC）
3. **执行请求并返回结果**

例如一个 PostgreSQL MCP Server 启动后会告诉 Client："我有一个 `query` 工具，接受一个 SQL 字符串参数，可以查询数据库。" 当 AI 决定要查数据库时，Client 就通过 MCP 协议调用这个 `query` 工具。

---

## 二、以 Supabase/PostgreSQL 为例的完整实践

### 场景

你有一个前端项目，使用 Supabase 作为后端数据库。你希望在 Cursor 中让 AI Agent 能够直接查询你的 Supabase 数据库——例如查看表结构、查询数据、帮你写 SQL。

### 方案选择

有两种常用的 MCP Server 可以连接 PostgreSQL/Supabase：

| MCP Server | 来源 | 特点 |
|------------|------|------|
| `@supabase/mcp-server-supabase` | Supabase 官方 | 专为 Supabase 设计，支持表管理、Edge Functions、Auth 等 Supabase 特有功能 |
| `@modelcontextprotocol/server-postgres` | MCP 官方 | 通用 PostgreSQL 连接器，纯 SQL 操作，更轻量 |

下面分别介绍。

---

### 方案 A：使用 Supabase 官方 MCP Server

#### 步骤 1：获取 Supabase 凭证

你需要两样东西：

- **Supabase Access Token**：在 https://supabase.com/dashboard/account/tokens 创建一个 Personal Access Token
- **Project ID**：你的 Supabase 项目 ID，在项目 Settings → General 中可以找到，格式如 `abcdefghijklmnop`

#### 步骤 2：在 Cursor 中配置 MCP Server

打开 Cursor 的 Settings → MCP，点击 "Add new MCP Server"，或者直接编辑项目根目录下的 `.cursor/mcp.json` 文件（全局配置则在 `~/.cursor/mcp.json`）：

```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": [
        "-y",
        "@supabase/mcp-server-supabase@latest",
        "--access-token",
        "<你的 Supabase Access Token>",
        "--project-ref",
        "<你的 Project ID>"
      ]
    }
  }
}
```

这段配置告诉 Cursor：
- 启动一个名为 `supabase` 的 MCP Server
- 通过 `npx` 运行 `@supabase/mcp-server-supabase` 这个 npm 包
- 传入访问令牌和项目 ID 作为参数
- 通信方式默认是 stdio（不用显式指定）

#### 步骤 3：重启 Cursor

配置保存后重启 Cursor（或重新加载窗口），Cursor 会自动启动这个 MCP Server 进程并与之建立连接。

#### 步骤 4：验证连接

在 Cursor Settings → MCP 页面，你应该能看到 `supabase` Server 的状态为绿色（已连接），并列出它提供的所有 Tools。

#### 可用工具一览

Supabase 官方 MCP Server 提供了非常丰富的工具：

```
数据库操作：
├── list_tables          列出所有表
├── list_extensions      列出已安装的扩展
├── list_migrations      列出迁移历史
├── apply_migration      应用新的迁移（DDL）
├── execute_sql          执行任意 SQL（读写皆可）
├── get_table_definition 获取表的完整定义（列、约束、索引等）

项目管理：
├── get_project_url      获取项目的 API URL
├── get_anon_key         获取匿名 API Key
├── list_projects        列出所有 Supabase 项目

Auth 管理：
├── list_auth_users      列出认证用户

Edge Functions：
├── list_edge_functions  列出边缘函数
├── deploy_edge_function 部署边缘函数

日志：
├── get_logs             查看项目日志

存储：
├── list_storage_buckets 列出存储桶

分支（Pro 功能）：
├── create_branch        创建数据库分支
├── list_branches        列出分支
├── ...
```

#### 步骤 5：在 Cursor 中使用

配置完成后，你就可以在 Cursor 的 Agent 模式中直接对话：

> **你**：查看一下我的 Supabase 数据库中有哪些表
>
> **Cursor AI**：（自动调用 `list_tables` 工具）你的数据库中有以下表：users, projects, analytics_events, ...
>
> **你**：查询 analytics_events 表中最近 7 天的数据，按 event_type 分组统计
>
> **Cursor AI**：（自动调用 `execute_sql` 工具，执行 `SELECT event_type, COUNT(*) FROM analytics_events WHERE created_at > NOW() - INTERVAL '7 days' GROUP BY event_type`）结果如下...

AI 会**自主决定**何时调用哪个工具，这就是 MCP + Agent 的魅力。

---

### 方案 B：使用通用 PostgreSQL MCP Server

如果你只需要纯 SQL 查询能力，或者想连接的是非 Supabase 的 PostgreSQL 数据库：

#### 步骤 1：获取数据库连接字符串

Supabase 的 PostgreSQL 连接字符串在：项目 Dashboard → Settings → Database → Connection string → URI

格式如下：

```
postgresql://postgres.[project-ref]:[password]@aws-0-[region].pooler.supabase.com:6543/postgres
```

#### 步骤 2：配置 MCP Server

在 `.cursor/mcp.json` 中：

```json
{
  "mcpServers": {
    "supabase-db": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://postgres.xxxx:password@aws-0-region.pooler.supabase.com:6543/postgres"
      ]
    }
  }
}
```

这个 Server 更轻量，通常提供以下工具：

```
├── query     执行只读 SQL 查询（SELECT）
├── list_tables  列出所有表
└── describe_table  查看表结构
```

注意这个 Server 默认**只允许只读查询**，比 Supabase 官方版更安全。

---

### 两种方案对比

| 维度 | Supabase 官方 MCP | 通用 PostgreSQL MCP |
|------|-------------------|---------------------|
| 安装 | `@supabase/mcp-server-supabase` | `@modelcontextprotocol/server-postgres` |
| 认证方式 | Access Token + Project Ref | 数据库连接字符串 |
| 工具数量 | 20+ 个（含 Auth、Storage、Edge Functions 等） | 2-3 个（纯 SQL 操作） |
| 写操作 | 支持（DDL + DML） | 默认只读 |
| 适用场景 | 深度 Supabase 集成 | 只需查询数据库 |
| 安全性 | 需妥善保管 Access Token | 需妥善保管连接字符串 |

---

## 三、MCP 的底层通信细节

为了让你更深入理解，下面展示一次完整的 MCP 调用在底层是如何发生的：

### 1. 初始化握手

Cursor 启动 MCP Server 后，先发送初始化请求：

```json
// Client → Server
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2024-11-05",
    "capabilities": { "tools": {} },
    "clientInfo": { "name": "cursor", "version": "0.45.0" }
  }
}

// Server → Client
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "protocolVersion": "2024-11-05",
    "capabilities": { "tools": { "listChanged": true } },
    "serverInfo": { "name": "supabase-mcp", "version": "1.0.0" }
  }
}
```

### 2. 发现可用工具

```json
// Client → Server
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/list"
}

// Server → Client
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "tools": [
      {
        "name": "execute_sql",
        "description": "Execute a SQL query against the Supabase database",
        "inputSchema": {
          "type": "object",
          "properties": {
            "query": {
              "type": "string",
              "description": "The SQL query to execute"
            }
          },
          "required": ["query"]
        }
      }
    ]
  }
}
```

这些工具信息会被注入到 LLM 的上下文中，让 AI 知道有哪些工具可用。

### 3. 调用工具

当 AI 决定要查询数据库时：

```json
// Client → Server
{
  "jsonrpc": "2.0",
  "id": 3,
  "method": "tools/call",
  "params": {
    "name": "execute_sql",
    "arguments": {
      "query": "SELECT count(*) FROM users WHERE created_at > '2025-01-01'"
    }
  }
}

// Server → Client
{
  "jsonrpc": "2.0",
  "id": 3,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "[{\"count\": 1523}]"
      }
    ]
  }
}
```

Server 执行 SQL，将结果返回给 Client，Client 再把结果送回 LLM，LLM 据此生成自然语言回答。

### 完整流转图

```
用户在 Cursor 中提问："数据库中有多少用户？"
    │
    ▼
Cursor 将问题 + 可用工具列表 发给 LLM
    │
    ▼
LLM 决定调用 execute_sql 工具，输出：
  {"tool": "execute_sql", "args": {"query": "SELECT count(*) FROM users"}}
    │
    ▼
Cursor（MCP Client）通过 MCP 协议调用 Server
    │  JSON-RPC via stdio
    ▼
MCP Server 连接 PostgreSQL，执行 SQL
    │
    ▼
MCP Server 返回结果：[{"count": 4231}]
    │
    ▼
Cursor 将结果送回 LLM
    │
    ▼
LLM 生成最终回答："您的数据库中目前有 4,231 个用户。"
    │
    ▼
用户看到回答
```

---

## 四、安全注意事项

1. **永远不要将 Access Token 或数据库连接字符串提交到 Git**。将 `.cursor/mcp.json` 加入 `.gitignore`，或使用环境变量方案

2. **最小权限原则**：如果只需要读取数据，使用只读的数据库角色连接；Supabase 的 Access Token 可以创建有限权限的

3. **生产数据库谨慎操作**：MCP 让 AI 可以直接执行 SQL，如果是生产数据库，建议使用只读连接或连接到只读副本

4. **环境变量方案**（推荐）：

```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": [
        "-y",
        "@supabase/mcp-server-supabase@latest",
        "--access-token",
        "${SUPABASE_ACCESS_TOKEN}",
        "--project-ref",
        "${SUPABASE_PROJECT_REF}"
      ]
    }
  }
}
```

然后在系统环境变量中设置 `SUPABASE_ACCESS_TOKEN` 和 `SUPABASE_PROJECT_REF`，而非硬编码在配置文件中。

---

## 五、MCP 生态现状

目前已经有大量现成的 MCP Server 可以直接使用：

| MCP Server | 功能 |
|------------|------|
| `server-postgres` | PostgreSQL 查询 |
| `server-sqlite` | SQLite 查询 |
| `server-filesystem` | 文件系统读写 |
| `server-github` | GitHub 操作（Issue、PR、搜索） |
| `server-slack` | Slack 消息收发 |
| `server-puppeteer` | 浏览器自动化 |
| `server-fetch` | HTTP 请求 |
| `server-memory` | AI 长期记忆存储 |
| `server-brave-search` | Brave 搜索引擎 |
| `server-google-maps` | Google 地图 |
| `@supabase/mcp-server-supabase` | Supabase 全功能 |
| `@upstash/mcp-server` | Redis/Kafka 操作 |

社区还在快速增长中。你也可以自己编写 MCP Server——本质上就是一个实现了 MCP 协议的 Node.js/Python 程序。