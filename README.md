# notwulin.github.io

Wu Lin 的个人网站与作品集仓库。

在线地址：<https://notwulin.github.io>

## 网站内容

这个网站主要展示四类内容：

- 关于我：产品判断、数据分析、AI 应用设计方向的个人介绍
- 作品集：RivalSense、DataMind等项目总览与案例页
- 经历与奖项：教育背景、实习经历、能力概览、竞赛与荣誉
- 兴趣与日常：长文写作，以及知识、体育、音乐相关的个人内容

## 项目特点

- 使用 GitHub Pages + Jekyll 部署
- 页面内容以 Markdown 为主，便于直接维护
- 自定义了 `page` layout，用于承载首页、作品集、经历、兴趣、博客等内容页

## 目录结构

- `index.md`：首页 / 关于我
- `portfolio.md`：作品集总览
- `projects/`：项目案例页
- `experience.md`：经历
- `awards.md`：奖项
- `hobbies.md`：兴趣
- `blogs.md`：博客列表
- `blogs/`：具体文章
- `_layouts/`：Jekyll layout
- `_includes/`：导航、页脚、头部、脚本等公共片段
- `assets/`：CSS、字体、JS
- `images/`：站点图片资源
- `file/`：公开附件

## 本地预览

如果本地已安装 Jekyll：

```bash
jekyll serve
```

默认本地访问地址通常为：

```bash
http://127.0.0.1:4000
```
