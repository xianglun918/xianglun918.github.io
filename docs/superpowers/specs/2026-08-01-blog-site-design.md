# xianglun918.github.io 博客站点 — 设计文档

- 日期：2026-08-01
- 状态：已确认
- 决策路径：superpowers brainstorming（含 Visual Companion 视觉确认）

## 背景与目标

重建个人 GitHub Pages 站点为纯博客，供作者（xianglun918 / 许相伦）管理与发布内容。内容方向为**混合（技术笔记 + 生活随笔）**。旧版内容（`ref/` 下 Jekyll 博客版与求职单页版）**不迁移**，全新开始，仅作为 about 页示例素材参考。

## 技术栈

| 项 | 决策 | 理由 |
|---|---|---|
| 生成器 | Jekyll | GitHub Pages 原生支持，git push 即发布，无构建服务器 |
| 主题 | **Chirpy**（远程主题） | 内置搜索/分类/标签/RSS/暗色/TOC，最贴近"现代卡片风 B"（白底+彩色渐变+圆角标签），中文资料多 |
| 域名 | 默认 `xianglun918.github.io` | 无需额外 DNS 配置，开箱即用 |
| 评论 | Giscus + 匿名留言表单 | Giscus 存 GitHub Discussions；匿名留言兜底无登录用户 |
| 搜索 | Chirpy 内置（lunr.js） | 构建自动索引，无需 API |
| RSS | jekyll-feed | 构建自动生成 `feed.xml` |
| 暗色模式 | Chirpy 自带日/夜切换 | 保留 |

## 站点结构

```
xianglun918.github.io/
├── _config.yml           # Chirpy 主题配置（giscus、feed、时区、站点标题/描述）
├── Gemfile               # jekyll + chirpy-theme 依赖
├── index.html            # 首页卡片流（Chirpy 内置 layout）
├── about.md              # 关于页（示例内容，取自 ref/ 个人简介素材）
├── _posts/               # 文章，命名 YYYY-MM-DD-标题.md
├── _includes/embed/      # 媒体嵌入封装
│   ├── bilibili.html     #   {% include embed/bilibili.html bvid="..." %}
│   ├── netease.html      #   {% include embed/netease.html id="..." %}
│   └── youtube.html      #   {% include embed/youtube.html id="..." %}
├── _includes/music-player.html  # 悬浮背景音乐播放器
├── assets/music/         # 本地背景音乐 mp3
├── assets/images/posts/  # 每篇文章一个图片子目录 assets/images/posts/<slug>/
└── tabs/                 # 分类、标签、归档（Chirpy 内置）
```

## 文章发布约定

- 文章路径：`_posts/2026-08-01-文章标题.md`
- frontmatter 声明 `title`、`date`、`categories`、`tags`
- 图片存放：`assets/images/posts/<文章slug>/`，正文用 Markdown 引用
- 媒体嵌入：使用 `_includes/embed/` 封装，播放器懒加载（文章打开才加载）
- 发布流程：`git push` → GitHub Actions 自动构建 → 上线

## 功能详情

### 评论：Giscus + 匿名留言
- Giscus：评论存仓库 GitHub Discussions，需要访客 GitHub 账号登录；需一次性开启仓库 Discussions 功能。
- 匿名留言：文章底部附无登录留言表单，提交存到接收方 issue。前置设置：确定接收仓库与 issue 配置。
- 留言与 Giscus 评论区并存，两套呈现区域。

### 站内搜索
- Chirpy 内置 lunr.js 搜索，构建时自动生成索引；中文分词较弱但对个人博客规模足够。

### 分类 + 标签
- frontmatter 声明后 Chirpy 自动生成 `/categories`、`/tags` 归档页。

### 图片管理
- 每篇文章一个目录 `assets/images/posts/<slug>/`，与文章一一对应，便于管理备份与未来迁移图床。

### 媒体嵌入
- B 站 / 网易云 / YouTube 三种 iframe 封装为 include，懒加载，调用简洁。

### 暗色模式
- 保留 Chirpy 日/夜切换。

### 背景音乐：悬浮播放器
- 页面角落固定的悬浮音乐播放器控件，支持播放/暂停/切歌。
- 音乐来源两种：本地 mp3（存 `assets/music/`）与网易云外链。
- 播放列表在 `_config.yml` 声明，通过 `_includes/music-player.html` 封装注入。
- 懒加载；浏览器自动播放限制下降级为静默待用户点击播放。

## 关于页（about.md）

占位示例内容，从 `ref/xianglun918.github.io_old/` 复制个人简介素材：姓名、教育背景（香港中文大学（深圳）电子信息工程学士、Texas A&M 电子工程硕士）、技能、项目简介、联系方式占位。后续可替换。

## 前置一次性设置（实施计划中需包含）

1. 开启 `xianglun918.github.io` 仓库 GitHub Discussions。
2. 确定匿名留言接收仓库（可为另一 repo 或同 repo issue），配置留言表单端点。
3. Chirpy 依赖安装：`bundle install`（Ruby/Bundler 环境）。

## 明确不做（YAGNI）

- 不迁移旧内容（保留在 `ref/` 存档）。
- 不接 CI/CD 自定义构建——依赖 GitHub Pages 官方 Jekyll 构建。
- 不部署图床/CDN（图片本地管理，量多后再迁）。
- 不做自建搜索服务（用 Chirpy 内置）。
