---
layout: page
permalink: /projects/rivalsense/index.html
title: RivalSense
page_class: case-study-page
side_nav_label: 导览
---

# RivalSense —— AI 竞品情报雷达

> 2026.03 - 2026.04｜独立开发 / 产品设计 / 全栈实现<br>
> [在线演示](https://rival-sense.vercel.app/) / [GitHub](https://github.com/notwulin/RivalSense)

## 项目概述

RivalSense 是一个面向产品经理的 AI 竞品情报系统，目标是把“到处找信息、手动去噪、再整理成报告”的竞品分析流程，收敛成一个可持续运行的情报雷达。

系统围绕三件事设计：

1. 从公开信源中持续发现竞品动态与用户反馈
2. 用本地规则和 NLP 先过滤噪声、压缩证据
3. 再让 AI 负责高层摘要，生成报告和每日简报

这不是一个单纯调用大模型的 Demo，而是一次完整的 AI 产品原型实践：从竞品管理、异步抓取、分析引擎、报告中心到部署演示都形成了闭环。

## 问题定义

产品经理做竞品分析时，经常遇到三个问题：

- **信源分散**：官网、RSS、社区、GitHub Issues、Reddit、Hacker News、Review site 等信息散落在不同平台
- **噪声很高**：大量内容只是正向宣传、低质量讨论或重复信息，不能直接进入分析
- **时效要求高**：竞品发布、涨价、宕机、安全事件等信号需要持续追踪，而不是一次性调研

RivalSense 的核心判断是：AI 不应该直接替代分析判断，而应该先成为一个“高召回、强去噪、可追溯”的过滤漏斗。

## 我的角色

- 独立完成产品定义、信息架构、交互流程和 MVP 范围控制
- 设计竞品管理、单竞品抓取、全量抓取、任务轮询、报告导出等核心流程
- 实现 Next.js 前端、Flask API、SQLite 数据模型和抓取分析流水线
- 构建本地 NLP 分析层，降低无关文本直接进入大模型导致的 Token 成本
- 设计 Vercel Demo Mode，使作品集演示不依赖后端冷启动、数据库或真实 API Key

## 技术栈

| Layer | Stack |
| --- | --- |
| Frontend | Next.js 16, React 19, Tailwind CSS 4, TypeScript |
| Backend | Flask 3.1, Flask-CORS, Gunicorn |
| Data | SQLite with WAL mode |
| Crawling | feedparser, BeautifulSoup, requests, lxml |
| NLP / Analysis | NLTK VADER, SnowNLP, jieba, scikit-learn, pandas, numpy |
| AI Summary | Gemini / DeepSeek, with local fallback |
| Deployment | Vercel Demo Mode, optional Render Flask backend |

## 系统架构

RivalSense 采用前后端分离架构：

```text
浏览器
  -> Next.js frontend
  -> Flask API
      -> routes/api.py
      -> services/jobs.py
      -> services/crawl_runner.py
      -> services/scraper.py
      -> services/discovery.py
      -> services/search_discovery.py
      -> services/relevance.py
      -> services/data_analyzer.py
      -> services/ai_analyzer.py
  -> SQLite database
```

核心数据流：

1. 用户添加竞品和显式来源
2. 前端触发单竞品或全量抓取任务
3. 后端创建 in-process background job，并返回 `job_id`
4. 前端把 active job 存入 `localStorage`，通过轮询恢复任务状态
5. 后台执行 `crawl -> relevance filtering -> persist -> analytics -> report`
6. 报告和 analytics 写入 SQLite，前端刷新 Dashboard 和 Reports

## 核心功能

### 1. 竞品管理与任务轮询

用户可以维护竞品、触发单竞品抓取，也可以执行全量抓取并生成每日简报。抓取任务走后台 job，状态包含 `queued`、`running`、`completed`、`failed`，前端通过 `/api/crawl-jobs/<job_id>` 展示进度。

为避免用户切页后丢失抓取状态，前端用 `localStorage` 保存 active job id：

- `rivalsense.activeCrawlAllJob`
- `rivalsense.activeCompetitorCrawlJobs`

### 2. 多信源数据发现

数据引擎遵循“广召回、强去噪、保留证据”的原则。已设计的来源类型包括：

- RSS / Atom feed
- 官网、博客、新闻页
- G2、App Store、Review site
- Reddit / Hacker News / GitHub Issues / StackOverflow
- Tavily、Brave Search、SerpAPI 等结构化 Search API
- 中文公开搜索结果：知乎、小红书、微博、B 站、贴吧、V2EX、掘金、36Kr、IT之家等

中文平台策略只通过搜索 API 发现公开页面，不绕过登录或反爬。

### 3. 本地去噪与相关性评分

在进入报告生成前，系统先对记录做本地过滤。每条记录会计算：

- `relevance_score`：竞品词在标题、正文、URL 中的相关性
- `pain_score`：bug、crash、too expensive、缺少、难用等痛点信号
- `business_score`：融资、发布、涨价、诉讼、安全事故等商业信号
- `positive_score`：正向但低决策价值内容
- `quality_score`：来源质量和内容长度
- `priority_score`：综合排序分
- `signal_intent`：pain / business / positive / discussion

这层设计的价值是：先用稳定、便宜、可解释的本地逻辑压缩噪声，再把更少、更相关的证据交给 AI。

### 4. Local-First 分析引擎

分析层使用本地 NLP 完成统计、情感分析、聚类和证据压缩：

- 英文情感分析：NLTK VADER
- 中文情感分析：SnowNLP
- 中文分词：jieba
- 关键词提取：TF-IDF
- 痛点聚类：KMeans
- 痛点分类：稳定性、性能、价格、功能缺口、易用性、账号登录、客服退款、隐私安全
- 商业信号识别：融资、定价、发布、人才、法务监管、安全事故

AI 只负责最后的高层摘要；如果未配置 Gemini / DeepSeek，系统会使用本地兜底报告。

## 核心接口

主要接口包括：

- `GET /api/health`
- `GET /api/competitors` / `POST /api/competitors`
- `POST /api/competitors/<id>/crawl-jobs`
- `POST /api/crawl-all-jobs`
- `GET /api/crawl-jobs/<job_id>`
- `GET /api/dashboard`
- `GET /api/reports`
- `POST /api/reports/<competitor_id>/export`
- `GET /api/briefs`

这些接口覆盖了从竞品管理、异步抓取、Dashboard、历史报告到每日简报的完整 MVP 流程。

## 结果与收获

RivalSense 的交付重点不是“抓了多少页面”，而是把竞品分析工作流拆成可运行、可解释、可展示的产品原型：

- 作品集入口使用 Vercel Demo Mode，用户打开即可看到完整交互
- 后端保留真实抓取和分析能力，可本地运行或部署到 Render
- 报告中心支持历史报告、每日简报和 Markdown 导出
- 数据引擎和分析引擎都有独立文档，便于后续迁移 PostgreSQL、外部队列和更稳定的抓取策略

## 当前限制

- 后台 job 当前存于 Flask 进程内存，后端重启后旧 `job_id` 会失效
- SQLite 适合本地和单进程 MVP，多实例生产环境需要迁移 PostgreSQL
- 真实抓取需要处理各平台速率限制、robots 合规、重试和 API Key Secret 管理
- 商业信号仍以规则匹配为主，适合作为雷达召回，不应直接当作最终事实

## 来源说明

本页内容主要整理自 RivalSense GitHub 源文件：

- `README.md`
- `docs/architecture.md`
- `docs/api.md`
- `docs/data-engine.md`
- `docs/analysis-engine.md`
- `docs/deployment.md`
- `docs/operations.md`
- `backend/requirements.txt`
- `frontend/package.json`
