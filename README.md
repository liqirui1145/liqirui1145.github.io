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

在文章页直接按 **Ctrl+P**（或点“打印本文”）即可。打印样式在 `assets/css/main.css` 末尾的 `@media print` 里：

- 全站正文、标题、打印区域统一使用 **微软雅黑**；代码保留等宽字体
- 隐藏页头、页脚、评论、侧栏目录、上下篇和按钮
- 自动展开外部链接 URL，正文两端对齐，A4 页边距
- 代码块、表格、图片避免跨页截断；代码块自动换行，背景强制为浅色
- 首页和文章列表打印为紧凑索引，只保留日期、标题、标签

## 页面结构

- 首页：全宽沙漠头图 + 个人资料卡 + 文章卡片列表
- 文章页：文章卡片 + 右侧本文目录（自动高亮）+ 评论区
- 全站使用微软雅黑，支持浅色/深色主题；深色主题下评论区同步切换
- 头图使用本地 SVG（`static/hero-desert.svg`），不依赖外部图片

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
  baseof.html         页面骨架（首页/文章页/普通页布局）
  home.html           首页（头图 + 文章卡片 + 侧栏）
  home.rss.xml        首页 RSS 模板
  list.html           列表页（分区）
  single.html         文章页（正文 + 目录 + 评论）
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
  hero-desert.svg     首页沙漠头图（本地 SVG）
hugo.toml             站点配置
```