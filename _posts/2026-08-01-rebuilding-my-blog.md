---
title: 花了一下午，把博客重新搭起来了
date: 2026-08-01
categories: [生活, 随笔]
tags: [博客, 建站, Chirpy, Jekyll]
description: 从 Ruby 升级到 GitHub Actions 部署，一次完整的博客重建记录。
---

## 起因

之前的博客还是好几年前搭的，一个 Jekyll + Architect 主题的站点，写了一篇文章就再也没更新过。后来又做了一个单页简历站，改了几版 CSS，投了几份简历，然后也荒废了。

最近突然又想写点东西。翻出那些老代码看了看——Ruby 2.6、Bootstrap 3、jQuery、还有一个早已挂掉的 `mail.php` 表单。算了，不如从头来。

## 选型

需求很简单：静态站点、Markdown 写作、免费托管、看着舒服。

没花多少时间就定了方案：

- **Jekyll** — 以前用过，不陌生
- **Chirpy 主题** — GitHub 上 star 最多的 Jekyll 主题之一，简洁干净，支持暗色模式
- **GitHub Pages** — 免费，自带 CI/CD，不需要买服务器
- **GitHub Actions 部署** — Chirpy v7 推荐的部署方式，push 即上线

不用 Hexo、不用 Hugo、不用 Next.js。年纪大了，不想折腾。

## 搭环境

Chirpy v7 要求 Ruby >= 3.1，我的 Mac 还是系统自带的 Ruby 2.6。先升级 Ruby：

```bash
brew install ruby@3.4
```

装完把 PATH 配好，`gem install bundler`，环境就算准备好了。顺手把 Ruby 4.0 给卸了——Chirpy 的 gemspec 里写的是 `~> 3.1`，4.x 不兼容。

然后从 chirpy-starter 拉模板，复制文件、合并 `.gitignore`、装 `assets/lib` 那些静态资源，`bundle install` 跑完，第一次 `jekyll build`——报错。

原来是 `ref/` 目录下的老站点文件被 Jekyll 当成了源文件处理，里面有 {% raw %}`{% link %}`{% endraw %} 标签引用不存在的文章。在 `_config.yml` 的 `exclude` 里加上 `ref` 就好了。果然，老代码的第一课永远是：别忘了改 exclude。

## 配站点

改 `_config.yml` 是最有成就感的部分——填上自己的名字、链接、邮箱，看着占位符一个个变成真实的内容。语言切成 `zh-CN`，时区设成 `Asia/Shanghai`，分享平台换成微博，联系人只留 GitHub 和邮件。

然后写了关于页，把那句"博文约礼"放了进去。校训这东西，读书的时候觉得矫情，毕业之后反而觉得挺有意思的。

## 加功能

功能是一点一点加上去的，按需求驱动：

1. **评论** — Giscus，基于 GitHub Discussions。API 拿到 repo ID 和 category ID，几行配置搞定。唯一的问题是 Giscus App 要手动装到仓库上，这个没法自动化。

2. **留言板** — 静态站没有后端，选了个 FormSubmit，表单 POST 过去自动转邮件。第一次用需要确认邮箱，之后就不用管了。

3. **背景音乐播放器** — 右下角一个悬浮按钮，CSS 画的 pulse 动画，点击播放/暂停。默认关着的，想用的话在 `_config.yml` 里把 `music.enabled` 改成 `true`，丢个 mp3 文件进去就行。

4. **网易云音乐嵌入** — Chirpy 自带 Bilibili、YouTube、Spotify 的嵌入，但没有网易云的。写了一个 Liquid include，支持单曲、歌单、专辑三种类型。

5. **图片目录约定** — `assets/img/posts/<slug>/`，一篇文章一个文件夹，简单清晰。

## 部署

GitHub Actions 的 workflow 是从 chirpy-starter 直接拿来的，改了几个 `paths-ignore` 加上了 `CLAUDE.md` 之类的非内容文件。把 GitHub Pages 的 source 从 legacy 切成 workflow，push 上去 —— 52 秒构建 + 部署完成，`https://xianglun918.github.io/` 就能访问了。

CI 里还有个 `htmlproofer` 步骤，用来检查死链。第一次跑就过了，说明没有 404。小成就感。

## 总结

整个流程差不多一个下午，大部分时间花在配环境和改配置上。真正写代码的部分没多少——Chirpy 这个主题确实做得很完善，开箱即用程度很高。

现在博客可以写东西了。第一件事当然是写这篇文章，记录一下这个过程。以后再回来看，应该会觉得挺好玩的。

下一个 flag：坚持更新。
