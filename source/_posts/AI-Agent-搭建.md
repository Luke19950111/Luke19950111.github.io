---
title: AI Agent 搭建
date: 2026-03-11 16:55:17
tags: [AI, Agent]
categories: AI
---

{% asset_img cover.jpg 为已有前端项目搭建 AI Agent 的操作步骤 %}

<!--more-->


## 一、AI Agent 是什么

**AI Agent（AI 智能体/代理）** 是一种能够自主感知环境、做出决策并执行行动以达成特定目标的人工智能系统。与传统的"一问一答"式 AI 对话不同，Agent 具备以下核心特征：

### 1. 核心组成

| 组件 | 说明 |
|------|------|
| **大语言模型（LLM）** | Agent 的"大脑"，负责理解、推理和决策。如 GPT-4、Claude、Gemini、DeepSeek 等 |
| **记忆系统（Memory）** | 短期记忆（当前对话上下文）和长期记忆（持久化知识库），使 Agent 能够在多轮交互中保持连贯 |
| **工具调用（Tools/Function Calling）** | Agent 可以调用外部工具，如搜索引擎、数据库查询、API 调用、代码执行器、文件操作等 |
| **规划能力（Planning）** | 将复杂任务分解为子任务，制定执行计划，按步骤完成 |
| **行动执行（Action）** | 根据规划结果，实际执行操作并观察结果 |
| **反思/自我纠错（Reflection）** | 评估执行结果，发现错误时自动调整策略 |

### 2. Agent 与普通 AI 对话的区别

**普通 AI 对话**（如 ChatGPT 基础模式）：用户提问 → 模型回答 → 结束。模型是被动的、无状态的、不能执行动作。

**AI Agent**：用户给出目标 → Agent 自主规划 → 调用工具执行 → 观察结果 → 继续推理 → 循环直到任务完成。Agent 是主动的、有状态的、能执行真实动作。

### 3. Agent 的工作循环（ReAct 模式）

```
用户输入目标
    ↓
思考（Reasoning）：分析当前状态，决定下一步
    ↓
行动（Action）：调用某个工具
    ↓
观察（Observation）：获取工具返回的结果
    ↓
再次思考 → 行动 → 观察... （循环）
    ↓
输出最终结果
```

### 4. 常见 Agent 架构模式

**单 Agent 模式**：一个 Agent 独立完成所有任务。适合简单场景。

**多 Agent 协作模式**：多个专门化 Agent 协作完成复杂任务。例如：一个 Agent 负责搜索，一个负责代码编写，一个负责审查。典型框架有 CrewAI、AutoGen、LangGraph 等。

**层级 Agent 模式**：一个"管理者"Agent 负责任务分配，多个"工人"Agent 负责具体执行。

### 5. 主流 Agent 框架

| 框架 | 特点 |
|------|------|
| **LangChain / LangGraph** | 最流行的 Agent 开发框架，支持复杂的工作流编排 |
| **Vercel AI SDK** | 面向前端/全栈开发者，与 Next.js 深度集成 |
| **OpenAI Agents SDK** | OpenAI 官方出品，支持 Handoff、Guardrails 等特性 |
| **CrewAI** | 多 Agent 协作框架，角色扮演模式 |
| **AutoGen (Microsoft)** | 微软的多 Agent 对话框架 |
| **Mastra** | TypeScript 优先的 Agent 框架，适合前端开发者 |
| **Dify / Coze** | 低代码/无代码 Agent 构建平台 |

---

## 二、为已有前端项目搭建 AI Agent 的操作步骤

以一个典型的 **React + Next.js 前端项目** 为例，集成一个具备工具调用能力的 AI Agent，采用 **Vercel AI SDK**（目前前端生态中最成熟的方案之一）。

### 场景假设

你有一个已有的前端项目，想为其添加一个 AI Agent 功能，例如：
- 智能客服助手（能查询订单、搜索知识库）
- 数据分析助手（能查数据库、生成图表）
- 项目管理助手（能创建任务、查询进度）

### 步骤 1：选择技术栈和模型供应商

```
决策清单：
├── LLM 供应商：OpenAI / Anthropic / 本地模型（Ollama）/ 国内模型（DeepSeek、通义千问）
├── Agent 框架：Vercel AI SDK（推荐前端项目）/ LangChain.js
├── 后端运行时：Next.js API Routes / Node.js Server / Edge Runtime
└── 前端 UI：自定义 Chat UI / @ai-sdk/react 的 useChat hook
```

### 步骤 2：安装依赖

```bash
# 核心 AI SDK
npm install ai @ai-sdk/openai

# 如果用 Anthropic（Claude）
npm install @ai-sdk/anthropic

# 如果用 zod 做工具参数校验（推荐）
npm install zod
```

### 步骤 3：配置环境变量

在项目根目录的 `.env.local` 中添加：

```env
# OpenAI
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxx

# 或 Anthropic
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxxxxx

# 如果使用国内模型的 OpenAI 兼容接口
OPENAI_BASE_URL=https://api.deepseek.com/v1
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxx
```

### 步骤 4：创建后端 Agent API 路由

在 `app/api/agent/route.ts`（Next.js App Router）中：

```typescript
import { openai } from '@ai-sdk/openai';
import { streamText, tool } from 'ai';
import { z } from 'zod';

export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = streamText({
    model: openai('gpt-4o'),
    system: `你是一个智能助手。你可以使用工具来帮助用户完成任务。
             在回答之前，先思考是否需要调用工具获取信息。`,
    messages,
    tools: {
      // 工具 1：查询天气
      getWeather: tool({
        description: '获取指定城市的天气信息',
        parameters: z.object({
          city: z.string().describe('城市名称'),
        }),
        execute: async ({ city }) => {
          // 实际项目中这里调用真实的天气 API
          const response = await fetch(
            `https://api.weather.example.com?city=${city}`
          );
          return response.json();
        },
      }),

      // 工具 2：查询数据库
      queryDatabase: tool({
        description: '查询业务数据库获取信息',
        parameters: z.object({
          query: z.string().describe('SQL 查询语句'),
        }),
        execute: async ({ query }) => {
          // 调用你的数据库查询服务
          // 注意：生产环境应做 SQL 注入防护
          const result = await dbClient.query(query);
          return result;
        },
      }),

      // 工具 3：搜索知识库
      searchKnowledge: tool({
        description: '在知识库中搜索相关文档',
        parameters: z.object({
          keyword: z.string().describe('搜索关键词'),
          limit: z.number().optional().default(5).describe('返回结果数量'),
        }),
        execute: async ({ keyword, limit }) => {
          // 调用向量数据库或全文搜索
          const results = await vectorDB.search(keyword, limit);
          return results;
        },
      }),
    },
    maxSteps: 5, // 允许 Agent 最多执行 5 轮工具调用循环
  });

  return result.toDataStreamResponse();
}
```

**关键概念说明：**

- `tools` 定义了 Agent 可以使用的工具集合，每个工具有描述、参数 schema 和执行函数
- `maxSteps` 控制 Agent 的推理-行动循环次数，防止无限循环
- `streamText` 以流式方式返回响应，用户可以看到实时生成过程
- Agent 会自主决定何时调用哪个工具，调用后将结果纳入上下文继续推理

### 步骤 5：创建前端 Chat UI

在 `app/agent/page.tsx` 中：

```tsx
'use client';

import { useChat } from '@ai-sdk/react';

export default function AgentPage() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } =
    useChat({
      api: '/api/agent',
      maxSteps: 5, // 前端也需要设置，以自动处理多步工具调用
    });

  return (
    <div className="mx-auto max-w-2xl p-4">
      <h1 className="mb-4 text-2xl font-bold">AI 智能助手</h1>

      <div className="mb-4 space-y-4">
        {messages.map((message) => (
          <div
            key={message.id}
            className={`rounded-lg p-3 ${
              message.role === 'user'
                ? 'ml-auto max-w-[80%] bg-blue-500 text-white'
                : 'mr-auto max-w-[80%] bg-gray-100'
            }`}
          >
            {/* 显示文本内容 */}
            {message.content && <p>{message.content}</p>}

            {/* 显示工具调用过程（可选，用于调试/透明度） */}
            {message.toolInvocations?.map((tool, i) => (
              <div key={i} className="mt-2 rounded bg-yellow-50 p-2 text-sm">
                <span className="font-mono text-yellow-700">
                  调用工具: {tool.toolName}
                </span>
                {tool.state === 'result' && (
                  <pre className="mt-1 text-xs text-gray-600">
                    {JSON.stringify(tool.result, null, 2)}
                  </pre>
                )}
              </div>
            ))}
          </div>
        ))}
      </div>

      <form onSubmit={handleSubmit} className="flex gap-2">
        <input
          value={input}
          onChange={handleInputChange}
          placeholder="问我任何问题..."
          className="flex-1 rounded-lg border p-3"
          disabled={isLoading}
        />
        <button
          type="submit"
          disabled={isLoading}
          className="rounded-lg bg-blue-500 px-6 py-3 text-white disabled:opacity-50"
        >
          {isLoading ? '思考中...' : '发送'}
        </button>
      </form>
    </div>
  );
}
```

### 步骤 6：增强 Agent 能力（进阶）

**6a. 添加记忆/上下文持久化：**

```typescript
// 使用数据库持久化对话历史
import { saveMessages, loadMessages } from '@/lib/chat-store';

export async function POST(req: Request) {
  const { messages, sessionId } = await req.json();

  // 加载历史消息
  const history = await loadMessages(sessionId);
  const allMessages = [...history, ...messages];

  const result = streamText({
    model: openai('gpt-4o'),
    messages: allMessages,
    tools: { /* ... */ },
    onFinish: async ({ text }) => {
      // 持久化新消息
      await saveMessages(sessionId, messages);
    },
  });

  return result.toDataStreamResponse();
}
```

**6b. 添加 RAG（检索增强生成）：**

```typescript
// 将用户上传的文档转为向量存储
import { embed } from 'ai';
import { openai } from '@ai-sdk/openai';

async function indexDocument(text: string) {
  const { embedding } = await embed({
    model: openai.embedding('text-embedding-3-small'),
    value: text,
  });
  await vectorStore.insert({ text, embedding });
}
```

**6c. 添加安全防护（Guardrails）：**

```typescript
const result = streamText({
  model: openai('gpt-4o'),
  messages,
  tools: { /* ... */ },
  // 输入防护：过滤不当请求
  experimental_prepareStep: async ({ messages }) => {
    const lastMessage = messages[messages.length - 1];
    if (containsSensitiveContent(lastMessage.content)) {
      throw new Error('请求包含不允许的内容');
    }
  },
});
```

### 步骤 7：测试和部署

```bash
# 本地开发测试
npm run dev

# 部署到 Vercel（如果使用 Next.js）
vercel deploy

# 或构建后部署到你的服务器
npm run build
npm start
```

### 整体架构图

```
┌─────────────────────────────────────────────┐
│                  前端 (React)                │
│  ┌─────────────────────────────────────┐    │
│  │   useChat() Hook                     │    │
│  │   - 管理消息状态                      │    │
│  │   - 处理流式响应                      │    │
│  │   - 自动处理多步工具调用               │    │
│  └──────────────┬──────────────────────┘    │
└─────────────────┼───────────────────────────┘
                  │ POST /api/agent
                  ▼
┌─────────────────────────────────────────────┐
│              后端 API Route                  │
│  ┌─────────────────────────────────────┐    │
│  │   streamText() + tools              │    │
│  │                                      │    │
│  │   思考 → 调用工具 → 观察 → 再思考    │    │
│  │         (Agent Loop)                 │    │
│  └──┬──────────┬──────────┬────────────┘    │
└─────┼──────────┼──────────┼─────────────────┘
      │          │          │
      ▼          ▼          ▼
  ┌───────┐ ┌────────┐ ┌──────────┐
  │ LLM   │ │ 数据库  │ │ 外部 API │
  │ API   │ │ 查询   │ │ 调用     │
  └───────┘ └────────┘ └──────────┘
```

---

## 总结

搭建 AI Agent 的核心要素就是三点：

1. **一个 LLM 作为大脑** — 负责理解意图、推理决策
2. **一组 Tools 作为双手** — 让 Agent 能够与外部世界交互
3. **一个循环机制作为行为模式** — 思考 → 行动 → 观察 → 再思考

Vercel AI SDK 把这三者封装得很好，对前端开发者非常友好。如果你的项目不是 Next.js，也可以用 LangChain.js 搭配任意后端框架（Express、Fastify 等）实现类似的效果。如果不想写代码，Dify、Coze 等平台提供了可视化搭建 Agent 的能力，搭建好后通过 API 集成到你的前端项目中即可。