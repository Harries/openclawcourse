# 第 57 讲：用 OpenClaw 做一份完整 PPT：从主题、提纲到 slide deck

很多人让 AI 做 PPT，最后只拿到一份“像大纲一样的幻灯片”。真正可用的 PPT，不只是把文字塞进页面，而是完成一条完整链路：明确听众、组织叙事、控制页数、设计版式、生成文件、检查视觉和事实。

## 用到的 Skill

优先到 [skills.lc](https://skills.lc/) 搜索 `presentation`、`slides`、`ppt`、`deck`。当前环境可对应使用 `presentations:Presentations` 生成和编辑 PowerPoint；用 `imagegen` 生成封面或场景图；用 `browser` 做资料查证；用 `spreadsheets` 生成图表数据；必要时用 `documents` 先写讲稿。

## 推荐流程

第一步，把需求写成 briefing：主题、听众、演讲时长、页数、风格、必须出现的观点、不能出现的内容。OpenClaw 先把 briefing 变成目录，而不是马上做文件。

第二步，生成 slide plan。每一页至少要有页标题、核心结论、主要图形、讲者备注。好的 PPT 是“一页一个判断”，不是“一页一堆材料”。

第三步，用 Presentations Skill 生成 `.pptx`。生成后不能直接交付，要渲染检查：标题是否溢出、图表是否清楚、页码是否连续、配色是否统一。

第四步，人工确认事实和口径。涉及客户、财务、竞品、法律和承诺的数据，必须回到原始来源核对。

## 实操练习

让 OpenClaw 做一份 10 页 PPT：《为什么企业需要 Agent 工作流》。要求第一页是标题页，最后一页是行动建议，中间必须包含一张“人工流程 vs Agent 流程”的对比图。

## 交付物

- `deck.pptx`
- `outline.md`
- `speaker-notes.md`
- `sources.md`

做 PPT 的关键不是“让 AI 排版”，而是让 OpenClaw 把资料、结构、视觉和验收串成一条可重复的生产线。
