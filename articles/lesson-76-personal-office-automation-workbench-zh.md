# 第 76 讲：综合实战：搭建一个个人办公自动化工作台

前面的实操课讲了 PPT、文档、表格、邮件、视频、调研、客服、销售、运营和会议。最终项目要把这些能力整合成一个个人办公自动化工作台。

## 用到的 Skill

优先到 [skills.lc](https://skills.lc/) 搜索 `office`、`automation`、`workflow`、`productivity`。当前环境可组合使用 `documents`、`presentations`、`spreadsheets`、`browser`、`himalaya`、`gog`、`apple-reminders`、`things-mac`、`trello`、`notion`、`imagegen` 和 `skill-creator`。

## 工作台结构

工作台至少包含五个入口：

1. 写作中心：文档、方案、报告。
2. 汇报中心：PPT、图表、会议材料。
3. 沟通中心：邮件、会议纪要、跟进。
4. 调研中心：网页调研、竞品分析、资料摘要。
5. 任务中心：待办、提醒、周期日报。

每个入口都不是一个聊天框，而是一条清晰 workflow：输入材料、调用 Skill、生成草稿、校验、人工确认、归档。

## 实施步骤

先列出你最常做的 10 个办公任务。再为每个任务定义输入、输出、使用 Skill 和风险边界。然后挑 3 个高频任务做成可复用模板。最后用 `skill-creator` 把稳定流程沉淀成自己的业务 Skill。

## 验收标准

一个合格的工作台，要能让你在一周内重复使用至少三次，并且每次都节省明确时间。它还应该留下记录：产物、来源、确认人和下一步任务。

最终目标不是让 OpenClaw 什么都做，而是让它把你的工作流变得更清晰、更稳定、更可复用。
