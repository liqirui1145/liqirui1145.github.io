# liqirui1145.github.io

个人技术博客，Hugo 框架，托管于 GitHub Pages。

## 本地预览

```bash
hugo server -D          # http://localhost:1313
```

## 写一篇新文章

```bash
hugo new content posts/文章文件名.md
```

然后编辑 `content/posts/文章文件名.md`，把 front matter 里的 `draft: true` 去掉即可发布。

## 发布

推送到 `main` 分支后，GitHub Actions 会自动构建并部署：

```bash
git add -A && git commit -m "post: xxx" && git push
```

首次使用需要在仓库 **Settings → Pages → Build and deployment → Source** 里选择 **GitHub Actions**。

## 评论

文章页使用 [giscus](https://giscus.app/) 评论，评论数据保存在仓库的 **GitHub Discussions** 里。

- 仓库需要开启 **Discussions**
- 需要把 **giscus** App 安装到此仓库
- 配置在 `hugo.toml` 的 `[params.giscus]`：
  - `repoid`：仓库 GraphQL ID
  - `categoryid`：Discussions 分类 ID
  - 这两个值可以在 [giscus.app](https://giscus.app/zh-CN) 的配置器里查到
- 评论只在 `content/posts/` 的文章页显示，`about` 等页面不显示

## 打印

文章页的“打印本文”按钮调用浏览器打印。打印样式在 `assets/css/main.css` 末尾的 `@media print` 里：

- 隐藏导航、页脚、评论、上下篇和按钮
- 正文切换为宋体/衬线字体，并自动展开外部链接 URL
- 代码块、表格、图片避免跨页截断；代码块自动换行

## 目录结构

```text
.github/
  workflows/
    deploy.yaml       GitHub Actions：构建并部署到 GitHub Pages
archetypes/
  default.md          新文章模板（hugo new 使用）
assets/
  css/
    main.css          全站样式（构建时压缩 + 指纹）
content/
  about.md            关于页
  posts/
    _index.md         文章分区说明页
    *.md              博客文章
  tags/
    _index.md         标签页说明
layouts/
  baseof.html         页面骨架
  home.html           首页（文章列表 + 分页）
  home.rss.xml        首页 RSS 模板
  list.html           列表页（分区）
  single.html         文章页
  taxonomy.html       标签总览页
  term.html           单个标签下的文章页
  404.html            404 页面
  robots.txt          robots.txt 模板
  _partials/
    head.html         <head>：标题、meta、样式引入
    header.html       顶部导航
    footer.html       页脚
    post-list.html    文章列表片段
    pagination.html   分页片段
    comments.html     giscus 评论（按需加载）
static/
  favicon.svg         站点图标（原样复制）
hugo.toml             站点配置
```