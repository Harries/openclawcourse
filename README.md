# OpenClaw 技术课程目录

这个目录不再按“90 天打卡”来规划，而是按一个更实用的目标组织：把 OpenClaw 的技术结构、运行链路、扩展方式、部署调试和真实应用讲清楚。

推荐阅读顺序：

```text
认识 OpenClaw
  -> 看懂一次请求如何流动
  -> 看懂模型、工具、浏览器、Workspace 的协作
  -> 学会 Skill / MCP / Plugin 扩展
  -> 学会部署、配置、调试
  -> 能做一个真实可运行的 OpenClaw 应用
```

## 第一部分：先建立整体地图

目标：回答“OpenClaw 到底是什么，它和普通 Agent、IDE Agent、自动化脚本有什么区别”。

- [x] 第 1 讲：[OpenClaw 是什么？为什么它不是普通 AI Agent](articles/lesson-01-openclaw-intro.md)
- [x] 第 2 讲：[Gateway、CLI、Bridge、Workspace 全部讲透](articles/lesson-02-gateway-cli-bridge-workspace.md)
- [x] 第 3 讲：[中文](articles/lesson-03-model-tools-browser-zh.md) / [English](articles/lesson-03-model-tools-browser-en.md)：OpenClaw 如何调用模型、工具和浏览器
- [x] 第 4 讲：[中文](articles/lesson-04-agent-platform-comparison-zh.md) / [English](articles/lesson-04-agent-platform-comparison-en.md)：OpenClaw vs OpenHands vs Hermes vs Claude Code

## 第二部分：从用户输入到任务执行

目标：沿着“一句话输入进去以后发生了什么”这条线，把内部链路讲清楚。

- [x] 第 5 讲：[中文](articles/lesson-05-agent-prompt-behavior-zh.md) / [English](articles/lesson-05-agent-prompt-behavior-en.md)：Agent Prompt 是怎么影响行为的
- [x] 第 6 讲：[中文](articles/lesson-06-user-input-internals-zh.md) / [English](articles/lesson-06-user-input-internals-en.md)：用户输入后内部发生了什么
- [x] 第 7 讲：[中文](articles/lesson-07-request-lifecycle-zh.md) / [English](articles/lesson-07-request-lifecycle-en.md)：一次 OpenClaw 请求的完整生命周期
- [x] 第 8 讲：[中文](articles/lesson-08-session-message-state-zh.md) / [English](articles/lesson-08-session-message-state-en.md)：会话、消息、上下文和任务状态如何组织
- [x] 第 9 讲：[中文](articles/lesson-09-prompt-priority-zh.md) / [English](articles/lesson-09-prompt-priority-en.md)：系统提示词、开发者指令和用户输入的优先级
- [x] 第 10 讲：[中文](articles/lesson-10-streaming-tools-state-zh.md) / [English](articles/lesson-10-streaming-tools-state-en.md)：流式输出、工具调用和中间状态如何返回给用户

## 第三部分：核心组件拆解

目标：不只知道名词，而是知道每个组件负责什么、不负责什么、和谁通信。

- [x] 第 11 讲：[中文](articles/lesson-11-gateway-dispatch-center-zh.md) / [English](articles/lesson-11-gateway-dispatch-center-en.md)：Gateway：OpenClaw 的入口层和调度中心
- [x] 第 12 讲：[中文](articles/lesson-12-cli-connect-openclaw-zh.md) / [English](articles/lesson-12-cli-connect-openclaw-en.md)：CLI：本地命令如何连接到 OpenClaw
- [x] 第 13 讲：[中文](articles/lesson-13-bridge-runtime-connection-zh.md) / [English](articles/lesson-13-bridge-runtime-connection-en.md)：Bridge：连接模型、工具和运行环境的中间层
- [x] 第 14 讲：[中文](articles/lesson-14-workspace-context-boundary-zh.md) / [English](articles/lesson-14-workspace-context-boundary-en.md)：Workspace：文件系统、项目上下文和执行边界
- [x] 第 15 讲：[中文](articles/lesson-15-permission-boundaries-zh.md) / [English](articles/lesson-15-permission-boundaries-en.md)：权限模型：Shell、Browser、文件读写的安全边界
- [x] 第 16 讲：[中文](articles/lesson-16-logs-observability-zh.md) / [English](articles/lesson-16-logs-observability-en.md)：日志与可观测性：如何看懂一次失败的调用

## 第四部分：模型接入与上下文工程

目标：讲清楚 OpenClaw 如何接入不同模型，以及上下文是怎么被组织、裁剪和传递的。

- [x] 第 17 讲：[中文](articles/lesson-17-provider-abstraction-zh.md) / [English](articles/lesson-17-provider-abstraction-en.md)：Provider 抽象：为什么 OpenClaw 可以接不同模型
- [x] 第 18 讲：[中文](articles/lesson-18-model-provider-setup-zh.md) / [English](articles/lesson-18-model-provider-setup-en.md)：OpenAI、Claude、Gemini 与 OpenAI Compatible 接入方式
- [x] 第 19 讲：[中文](articles/lesson-19-model-selection-tradeoffs-zh.md) / [English](articles/lesson-19-model-selection-tradeoffs-en.md)：模型选择：速度、成本、上下文长度和工具能力
- [x] 第 20 讲：[中文](articles/lesson-20-context-assembly-zh.md) / [English](articles/lesson-20-context-assembly-en.md)：上下文组装：文件、历史消息、指令和工具 schema
- [x] 第 21 讲：[中文](articles/lesson-21-tool-call-protocol-zh.md) / [English](articles/lesson-21-tool-call-protocol-en.md)：工具调用协议：模型如何决定调用哪个工具
- [x] 第 22 讲：[中文](articles/lesson-22-failover-retry-strategy-zh.md) / [English](articles/lesson-22-failover-retry-strategy-en.md)：模型降级、重试和错误处理策略

## 第五部分：工具系统、Browser、Shell 与 Canvas

目标：把 OpenClaw 最有实际价值的执行能力讲清楚：它怎样从“会聊天”变成“能做事”。

- [x] 第 23 讲：[中文](articles/lesson-23-browser-shell-canvas-principles-zh.md) / [English](articles/lesson-23-browser-shell-canvas-principles-en.md)：Browser、Shell、Canvas 原理
- [x] 第 24 讲：[中文](articles/lesson-24-minimal-agent-zh.md) / [English](articles/lesson-24-minimal-agent-en.md)：手写一个最小 Agent
- [x] 第 25 讲：[中文](articles/lesson-25-shell-tool-long-tasks-zh.md) / [English](articles/lesson-25-shell-tool-long-tasks-en.md)：Shell Tool：命令执行、输出读取和长任务管理
- [x] 第 26 讲：[中文](articles/lesson-26-browser-tool-automation-zh.md) / [English](articles/lesson-26-browser-tool-automation-en.md)：Browser Tool：网页打开、点击、输入、截图和验证
- [x] 第 27 讲：[中文](articles/lesson-27-canvas-artifact-output-zh.md) / [English](articles/lesson-27-canvas-artifact-output-en.md)：Canvas / Artifact：把结果变成可查看、可交互的产物
- [x] 第 28 讲：[中文](articles/lesson-28-tool-failure-recovery-zh.md) / [English](articles/lesson-28-tool-failure-recovery-en.md)：工具失败时怎么办：重试、回滚、人工确认和风险提示

## 第六部分：Skill、MCP 与插件扩展

目标：讲清楚 OpenClaw 的扩展机制，读者要能自己装 Skill、写 Skill、接 MCP、做插件。

- [x] 第 29 讲：[中文](articles/lesson-29-use-openclaw-skill-zh.md) / [English](articles/lesson-29-use-openclaw-skill-en.md)：如何使用 OpenClaw Skill
- [x] 第 30 讲：[中文](articles/lesson-30-find-openclaw-skills-zh.md) / [English](articles/lesson-30-find-openclaw-skills-en.md)：OpenClaw Skill 从哪里找
- [x] 第 31 讲：[中文](articles/lesson-31-skill-structure-zh.md) / [English](articles/lesson-31-skill-structure-en.md)：Skill 的结构：什么时候该写成说明、脚本或资源文件
- [x] 第 32 讲：[中文](articles/lesson-32-skill-triggering-zh.md) / [English](articles/lesson-32-skill-triggering-en.md)：Skill 的触发：如何让 Agent 在正确场景使用正确能力
- [x] 第 33 讲：[中文](articles/lesson-33-mcp-basics-zh.md) / [English](articles/lesson-33-mcp-basics-en.md)：MCP 基础：Server、Tool、Resource 和 Prompt
- [x] 第 34 讲：[中文](articles/lesson-34-external-system-mcp-tool-zh.md) / [English](articles/lesson-34-external-system-mcp-tool-en.md)：把一个外部系统接成 MCP 工具
- [x] 第 35 讲：[中文](articles/lesson-35-plugin-basics-zh.md) / [English](articles/lesson-35-plugin-basics-en.md)：Plugin 基础：把 Skill、MCP 和前端能力打包
- [x] 第 36 讲：[中文](articles/lesson-36-plugin-permissions-upgrades-zh.md) / [English](articles/lesson-36-plugin-permissions-upgrades-en.md)：插件权限、安装、升级和版本兼容

## 第七部分：部署、配置与调试

目标：让读者能把 OpenClaw 跑起来、配明白、坏了能查。

- [ ] 第 37 讲：本地安装与最小可运行配置
- [ ] 第 38 讲：Docker / docker-compose 部署
- [ ] 第 39 讲：配置文件、环境变量和 Provider 密钥管理
- [ ] 第 40 讲：端口、反向代理、HTTPS 和内网访问
- [ ] 第 41 讲：Workspace 挂载、数据目录和持久化
- [ ] 第 42 讲：doctor / debug：常见错误如何定位
- [ ] 第 43 讲：性能问题：慢请求、长上下文、CPU 占用和并发任务
- [ ] 第 44 讲：升级与迁移：版本变化时怎么保护配置和数据

## 第八部分：真实场景实战

目标：用几个完整案例，把前面的技术串起来。

- [ ] 第 45 讲：企业微信 / Telegram / WhatsApp 接入思路
- [ ] 第 46 讲：网页自动化助手：从需求到 Browser 执行链路
- [ ] 第 47 讲：知识库问答：RAG、文件索引和权限边界
- [ ] 第 48 讲：数据分析助手：文件读取、脚本执行和结果可视化
- [ ] 第 49 讲：多 Agent 与任务队列：什么时候需要拆分任务
- [ ] 第 50 讲：SaaS 化改造：用户、租户、额度、审计和隔离

## 第九部分：从理解到创造

目标：最终不是只会使用 OpenClaw，而是能基于 OpenClaw 设计自己的 Agent 系统。

- [ ] 第 51 讲：如何设计一个可靠的 Agent 工作流
- [ ] 第 52 讲：如何为业务写高质量 Skill
- [ ] 第 53 讲：如何评估一个 Agent 的成功率和风险
- [ ] 第 54 讲：如何把 OpenClaw 能力嵌入自己的产品
- [ ] 第 55 讲：最终项目：做一个可部署的 OpenClaw 业务助手

## 建议写作节奏

后续文章不用强行追求天数，可以按“模块完整度”推进：

- 已完成的文章继续保留原文件名，避免链接失效。
- 新文章统一使用 `lesson-XX-xxx-zh.md` / `lesson-XX-xxx-en.md` 命名，`XX` 必须和目录里的讲次一致。
- 如果某一讲内容很大，可以拆成上下篇；如果某一讲内容很小，可以合并到相邻主题。
- 每篇文章都要回答三个问题：这个概念解决什么问题、它在 OpenClaw 里处于什么位置、读者如何验证自己真的理解了。
