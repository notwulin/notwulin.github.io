---
layout: page
permalink: /projects/datamind/index.html
title: DataMind
page_class: case-study-page
side_nav_label: 导览
---

# DataMind —— 多智能体自动化数据分析平台

> 2026.01 - 2026.02｜独立开发 / 产品设计 / Agent 工作流实现<br>
> [在线演示](https://datamind-multi-agent.streamlit.app/) / [GitHub](https://github.com/notwulin/DataMind)

## 项目概述

DataMind 是一个基于 Streamlit 和 LangGraph 的自动化数据分析辅助平台，目标是让非技术用户通过上传数据和自然语言交互，完成从数据检测、清洗、探索性分析到报告生成的流程。

我把这个项目定义为 “Chat-as-a-Report”：用户不需要先理解 Notebook、SQL 或 BI 工具，而是通过对话和一键 Pipeline 得到结构化分析反馈。

## 问题定义

很多业务用户有数据分析需求，但他们面对原始 CSV 时会卡在几个环节：

- 不知道先清洗、先画图还是先建模
- 不知道异常值、缺失值、重复值会怎样影响结论
- 不知道应该选择柱状图、折线图、相关性图还是漏斗分析
- 即使生成了图，也很难把图表转化成业务判断

DataMind 的设计目标不是让 AI “替用户随便画图”，而是把数据分析最佳实践沉淀为可执行的 Agent 流程。

## 我的角色

- 独立完成产品定位、交互流程、页面结构和 Agent 功能拆分
- 基于 LangGraph 设计 Auto-Pipeline 状态机
- 实现数据清洗、EDA、领域分析、A/B 测试、报告导出等模块
- 定制 Streamlit 多页应用和视觉层级，弱化默认 Streamlit 工具感
- 使用 sample ecommerce dataset 验证上传、分析、图表和报告链路

## 技术栈

| Layer | Stack |
| --- | --- |
| App Framework | Streamlit 1.32+ |
| Agent Orchestration | LangGraph 0.2+, LangChain 0.2+ |
| LLM Adapter | langchain-google-genai, `.env` configurable |
| Data Processing | pandas, numpy, scipy, statsmodels, scikit-learn |
| Visualization | Plotly |
| File Handling | openpyxl, CSV upload |
| Runtime | Python 3.11+ |

## 产品结构

项目采用 Streamlit 多页架构：

```text
app.py
pages/
  1_数据清洗.py
  2_数据探索.py
  3_用户分析.py
  4_AB测试.py
  5_AI对话.py
  6_报告导出.py
agents/
  router.py
  pipeline.py
  cleaning_agent.py
  eda_agent.py
  analyst_agent.py
  ab_agent.py
tools/
  cleaning.py
  eda.py
  user_analysis.py
  ab_testing.py
  advanced_stats.py
  report_generator.py
utils/
  data_store.py
  llm_factory.py
  ui_enhancer.py
```

## 智能体架构

DataMind 的 Agent 设计分为两层：自动分析 Pipeline 和对话 Router。

### 自动分析 Pipeline

`agents/pipeline.py` 使用 LangGraph `StateGraph`，将一键自动分析拆成五个节点：

```text
START
  -> profiling
  -> cleaning
  -> eda
  -> analysis
  -> report
END
```

每个节点职责明确：

- **Profiling**：读取列名、数据类型和前 5 行样例，识别业务场景
- **Cleaning**：执行缺失值、重复值、异常值诊断和清洗建议
- **EDA**：生成探索性统计、相关性分析和异常洞察
- **Analysis**：基于业务模板生成深度分析
- **Report**：整合数据概况、清洗结果、EDA 发现和业务建议，导出 Markdown 报告

### Router Agent

`agents/router.py` 负责识别用户意图，并把自然语言问题路由到不同 Agent：

- `cleaning`：数据清洗、缺失值、异常值、质量评分
- `eda`：数据概况、分布、相关性、探索分析
- `analyst`：RFM、Cohort、LTV、漏斗、中介效应、调节效应
- `ab_test`：A/B 测试、假设检验、p 值、样本量、统计显著性
- `general`：通用问答和功能引导

这使用户既可以走“一键自动分析”，也可以在 AI 对话页提出局部问题。

## 领域模板

Pipeline 会先判断数据属于哪类业务场景，再加载对应分析重点：

- **电商交易分析**：品类销售额、客单价、复购率、高价值用户、退货/取消率
- **营销投放分析**：渠道 ROI、CAC、转化漏斗、投放时段、异常渠道
- **用户增长分析**：新用户增长、活跃分层、留存曲线、流失画像、行为转化
- **通用数据分析**：数值分布、类别不均衡、高相关变量、缺失模式、指标关联

这个设计避免了所有数据都走同一套泛化回答，让报告更接近业务分析语境。

## 核心功能

### 1. 数据质量检查与清洗

系统提供缺失值、重复值、异常值、数据质量评分等基础诊断，并将清洗结果保存在共享数据状态中，供后续 EDA 和报告复用。

### 2. 自动 EDA 与图表洞察

EDA 工具会运行统计摘要、异常洞察和相关性分析，并用 Plotly 生成交互式可视化，减少用户在图表类型选择上的认知负担。

### 3. 领域专项分析

当系统识别出电商、营销或用户增长数据后，会把 EDA 发现和数据质量信息放入领域分析 Prompt 中，生成 3-5 条核心洞察和 2-3 条行动建议。

### 4. A/B 测试与高级统计

项目保留独立的 A/B 测试页面和 `tools/ab_testing.py`，支持假设检验、均值检验、频率测试、样本量和功效相关分析。

### 5. 报告导出

`tools/report_generator.py` 会把数据画像、清洗报告、EDA 洞察、领域分析结果整合为 Markdown 报告，适合继续沉淀为业务分析文档。

## 版本演进

项目版本演进体现了从单体 Demo 到 Agent 工作流的变化：

- **v1.0**：Streamlit 上传、解析和简易问答 MVP
- **v1.5**：加入基础 EDA、相关性分析、Plotly 图表、数据状态评分卡
- **v2.1**：多页架构、A/B 测试、RFM、LTV、Cohort 等高级分析
- **v3.0**：重构为 LangGraph 多 Agent 架构，加入 Auto-Pipeline 和 UI Rework

## 结果与收获

DataMind 最重要的收获不是“让模型回答数据问题”，而是验证了一个更可控的 AI 数据产品形态：

- 用 Agent 拆分数据分析中的稳定步骤
- 用 Python 工具执行计算，避免 LLM 编造数字
- 用 LLM 做解释、总结和下一步建议
- 用报告导出承接用户的最终决策场景

## 来源说明

本页内容主要整理自 DataMind GitHub 源文件：

- `README.md`
- `CHANGELOG.md`
- `requirements.txt`
- `agents/pipeline.py`
- `agents/router.py`
- `tools/`
- `pages/`
