---
layout: page
permalink: /projects/mishijian/index.html
title: 米事见
page_class: case-study-page
side_nav_label: 导览
---

# 米事见 —— 舆情与用户洞察数据产品

> 2024.04 - 2024.06｜项目负责人 / 产品 PM（5 人团队）

## 项目概述

米事见是围绕小米舆情分析场景设计的数据产品。项目从微博数据采集、清洗、分析建模到可视化看板，形成了一套面向品牌舆情和用户洞察的分析链路。

这个项目不是单独做一个看板，而是把“帖子内容、话题变化、用户画像、情绪倾向、热点事件、词频网络”组织为可查询、可解释、可展示的产品系统。

## 问题定义

舆情分析的难点不在于“画出很多图”，而在于如何从大量碎片化文本中回答清楚：

- 最近用户主要在讨论什么？
- 哪些话题正在升温？
- 用户情绪是正向、负向还是中性？
- 哪些用户或博主对传播有更大影响？
- 哪些内容值得品牌进一步追踪和解释？

米事见的目标是把这些问题落到可查询的数据模块和可视化分析页面中。

## 我的角色

- 作为项目负责人，主导 5 人团队完成需求拆解、模块规划和交付节奏管理
- 规划帖子分析、话题分析、用户分析、热点内容、权威博主等核心模块
- 参与数据采集、清洗、建模、后端接口和前端可视化联调
- 输出动态可视化看板和舆情分析报告
- 项目获得“优秀负责人”“优秀项目管理”称号

## 技术栈

| Layer | Stack |
| --- | --- |
| Data Collection | Python, Selenium |
| Backend | Java 19, Spring Boot 3.0.2, Spring Web |
| ORM / Pagination | MyBatis, PageHelper |
| Database | MySQL |
| Frontend | Vue 3, Vite, Vue Router |
| UI / Visualization | Element Plus, ECharts, vue-echarts, Bootstrap |
| HTTP | Axios |
| Modeling / Analysis | TF-IDF, LDA, 共现网络, BERT 情感分析 |

## 数据链路

项目的数据链路可以拆成四层：

```text
Weibo public data
  -> Python + Selenium collection
  -> cleaning and modeling
  -> MySQL tables
  -> Spring Boot + MyBatis API
  -> Vue 3 + ECharts dashboard
```

项目共抓取 **28w+** 条微博相关数据，清洗后保留 **16w+** 条，用于后续帖子、话题、用户和情感分析。

## 后端架构

后端采用 Spring Boot + MyBatis，按业务域拆分 Controller / Service / Mapper / Entity。

主要实体包括：

- `contents`：帖子内容、转发、评论、点赞、日期、话题分类
- `topic`：话题信息和日期聚合
- `userinfo`：用户与博主信息
- `Useranalysis`：用户活跃度、情感、标签、性别、年龄、IP 等分析结果
- `hot`：热点话题、热点内容、Top 用户、话题搜索结果
- `wordfreq`：词频结果
- `wordnet`：词共现网络

主要接口模块：

- `/contents/*`：帖子列表、分页搜索、帖子数量、帖子主题分类
- `/topic/*`：话题总数、话题列表、模糊查询、日期聚合
- `/useranalysis/*`：用户活跃度、情感分析、情感词频、IP、标签、性别、年龄
- `/hot/*`：热点内容、Top 用户、类型统计、关键词话题、话题内容
- `/freq/*`：词频、热门词
- `/wordnet/*`：词共现网络

## 前端结构

前端使用 Vue 3 + Vite，并通过 Vue Router 组织页面：

- `Welcome.vue`：数据概览与入口
- `contents.vue`：帖子内容与发帖趋势
- `topic.vue`：话题管理与话题趋势
- `User.vue`：用户画像、情感与活跃度
- `Admin.vue`：后台管理
- `ContentsChart.vue` / `TopicChart.vue`：ECharts 图表组件

图表层使用 ECharts，并支持 tooltip、坐标轴切换、保存图片等交互能力。

## 分析模块

### 1. 帖子分析

围绕帖子内容、发帖时间、转发数、评论数和点赞数进行聚合，支持按内容检索和分页查看。前端通过发帖概况图展示每日帖子量变化。

### 2. 话题分析

对微博话题进行聚合和检索，支持按关键词查找相关话题，并进一步查看该话题下的帖子内容。

### 3. 用户画像

用户分析模块覆盖：

- 每日用户发帖量作为活跃度
- 情感分析结果分布
- 情感关键词词频
- IP 地域信息
- 用户标签
- 性别和年龄分布

### 4. 词频与共现网络

词频模块输出高频词和最新热门词；共现网络用于观察关键词之间的关联关系，帮助从文本中识别热点主题结构。

### 5. 舆情建模

分析建模使用 TF-IDF、LDA、共现网络和 BERT 情感分析，把大量文本转化为主题、情绪和传播结构。

## 结果与收获

米事见的交付成果包括：

- 舆情分析动态看板
- 帖子、话题、用户和热点内容查询模块
- 词频与共现网络可视化
- 用户情感与画像分析
- 舆情分析报告
- 28w+ 原始数据与 16w+ 清洗数据支撑的分析链路

这个项目让我完整经历了数据产品从 0 到 1 的过程：不仅要完成技术实现，还要把数据链路、业务问题和结果表达组织成用户能理解的产品。

## 来源说明

本页内容主要整理自米事见项目源文件：

- `mi_end/pom.xml`
- `mi_end/src/main/java/com/example/xiaomi/controller/*`
- `mi_end/src/main/java/com/example/xiaomi/entity/*`
- `mi_end/src/main/resources/application.properties`
- `mi_end_web/package.json`
- `mi_end_web/src/router/index.js`
- `mi_end_web/src/views/*`
- `mi_end_web/src/views/components/*`
- `mi_end_web/DESIGN_SYSTEM.md`
