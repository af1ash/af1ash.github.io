# 个人文章分享

基于 Hexo 8.1.2，使用 Stellar 2.0.0（npm 依赖锁定版本）。

## 本地运行

需要 Node.js 22 或以上。

```sh
npm ci
npm run server
```

构建静态站点：

```sh
npm run clean
npm run build
```

站点配置位于 `_config.yml`，主题覆盖配置位于 `_config.stellar.yml`。
旧主题配置保留，可供迁移参考。文章和原有链接结构保持不变。

GitHub Actions 使用 Node.js 22，通过 `npm ci` 安装锁定依赖并部署 GitHub Pages。

主题文档：https://xaoxuu.com/wiki/stellar/

## 文章日期

文章 Front Matter 必须填写 `date`，避免 GitHub Actions 使用检出当天的文件时间。
`npm exec hexo new "文章标题"` 会自动填写发布日期。

```yaml
---
title: 文章标题
date: 2024-06-09 11:28:22
updated: 2026-11-26 17:00:00
---
```

`updated` 可选，实际修改文章时手动填写；省略时沿用发布日期。时间采用 Asia/Shanghai。
