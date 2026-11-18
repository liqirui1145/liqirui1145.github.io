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

## 目录结构

```text
content/          文章（Markdown）
  posts/          博客文章
  about.md        关于页
layouts/          模板
  baseof.html     页面骨架
  home.html       首页
  list.html       列表页（分区 / 标签）
  single.html     文章页
  _partials/      头部、页脚、文章列表等片段
assets/css/       样式（构建时自动压缩 + 指纹）
static/           原样复制的静态文件（favicon 等）
hugo.toml         站点配置
```