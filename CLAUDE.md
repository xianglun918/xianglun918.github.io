# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库定位

`xianglun918.github.io` 是用户（xianglun918 / lun）的个人网站仓库，托管于 GitHub Pages（CNAME 曾指向 `xianglun918.xyz`）。当前根目录基本是空壳（仅 `README.md` 占位、`ref/` 历史存档、`.omc/` 状态目录），尚无新的站点实现。

## ref/ —— 历史版本存档（只读参考）

`ref/` 下存放两代旧版个人网站，作为未来重建时的**内容与设计参考**，改动前先在这里找素材：

- **`ref/xianglun918.github.io_backup/`** — Jekyll 博客版（约 2021-08）。`jekyll-theme-architect` 主题，`_config.yml` 配站点标题，`index.md` 为首页，`_posts/` 下是 markdown 博客（目前仅 `FirstPost.md`）。这类站点用 `jekyll serve` 即可本地预览。
- **`ref/xianglun918.github.io_old/`** — 更早的单页求职简历主页（纯 HTML/CSS/JS，Bootstrap + jQuery）。含个人信息、技能条、教育/项目时间线、项目展示（raspCar、人脸轮廓提取、京东机器人挑战赛等）、联系方式和第一版简历 PDF。内容以中文为主。

两版均绑过自定义域名 `xianglun918.xyz`；外链指向 GitHub（`xianglun918`）和 CSDN（`xuxl97`）。

## 约定

- 站点内容全部使用简体中文（含代码注释与页面文案），语气口语化、带作者个人风格。
- 静态托管在 GitHub Pages：不要引入需要服务端运行的能力（旧版 `mail.php` 正是因此不可用，表单需改为 mailto 或第三方表单服务）。
- 历史版本目录 `ref/` 是存档，不要在其中直接改内容；需要复用素材时复制到新的站点结构中再编辑。
