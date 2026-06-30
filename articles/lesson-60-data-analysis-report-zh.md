# 第 60 讲：用 OpenClaw 做数据分析报告：CSV / Excel 到可视化结果

数据分析报告和普通表格处理的区别在于：报告必须围绕业务问题。CSV 或 Excel 只是原材料，真正的产物是“可行动的判断”。

## 用到的 Skill

优先到 [skills.lc](https://skills.lc/) 搜索 `data analysis`、`csv`、`visualization`、`python`。当前环境可对应使用 `spreadsheets:Spreadsheets` 处理表格，使用 Shell 执行分析脚本，使用 `documents` 写报告，使用 `presentations` 做汇报页。

## 执行链路

先定义问题：比如“过去三个月转化率下降的原因是什么”，而不是“分析这个表”。问题决定指标、分组、图表和解释方式。

再建立数据字典：每一列代表什么，单位是什么，是否可为空，和业务流程的哪个环节对应。没有数据字典，后面的分析容易变成猜谜。

然后做探索性分析：总体趋势、分组对比、异常点、漏斗、留存或相关性。OpenClaw 可以生成脚本和图表，但需要把每一步变成可复查的输出。

最后写报告：背景、数据范围、关键发现、证据图表、建议动作、风险和下一步验证。

## 人工确认点

不要把相关性写成因果；不要忽略样本量；不要隐藏过滤条件；不要只报好看的图。

## 实操练习

准备一个 `orders.csv`，让 OpenClaw 输出 `analysis.ipynb`、`charts/`、`report.md` 和 `executive-summary.md`。

数据分析的价值不在图表数量，而在能不能让团队下一步动作更清楚。
