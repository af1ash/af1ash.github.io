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

## 添加文章

以下命令均在项目根目录执行。

1. 创建文章，建议使用英文文件名，便于保持链接简洁：

   ```sh
   npx hexo new post "my-new-post"
   ```

   会生成 `source/_posts/my-new-post.md`，并自动填写发布日期。

2. 编辑生成的 Markdown 文件，将 `title` 改为实际标题，在 Front Matter 下方编写正文：

   ```markdown
   ---
   title: 我的新文章
   date: 2026-11-26 18:00:00
   tags:
     - Python
   categories:
     - 技术笔记
   ---

   这里是文章正文。

   ## 小节标题

   使用 Markdown 编写内容。
   ```

   保留命令生成的 `date`；上面的日期仅为示例。`tags` 和 `categories` 可按需填写。
   如需插图，可将图片放入 `source/images/`，在正文中使用 `![图片说明](/images/example.png)` 引用。

3. 启动本地预览：

   ```sh
   npm run server
   ```

   打开 http://localhost:4000/ 检查文章，按 `Ctrl+C` 停止服务。

4. 构建并确认没有报错：

   ```sh
   npm run clean
   npm run build
   ```

5. 提交文章和引用的图片，再推送到 `master`：

   ```sh
   git add source/_posts/my-new-post.md
   # 如果新增了图片，也需要暂存对应文件：
   # git add source/images/example.png
   git commit -m "docs: 添加我的新文章"
   git push origin master
   ```

   GitHub 仓库的 Actions 页面中，等待 `Pages` 工作流成功后，访问 https://af1ash.github.io/ 查看文章。
   发布由 GitHub Actions 自动构建完成，无需手动运行 `hexo deploy`。

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
