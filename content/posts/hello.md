---
title: "开张：把折腾记下来"
slug: "hello"
date: 2026-11-15T10:00:00+08:00
description: "为什么开这个站，以及这里打算写些什么。"
tags: ["随笔"]
---

断断续续在本地写了不少笔记，散在 `~/文档` 的各个角落，写完就忘。索性开个站，把还能复现的东西放上来。

这里大概会写三类东西：

- **Linux 上踩到的坑**：配置改了不生效、日志里只有一句 `Permission denied` 的那种。
- **shell 脚本**：尤其是自包含安装器这类"要交付给别人用"的写法。
- **时间同步**：chrony、SNTP、NTP 服务器那些事。

## 这个站怎么来的

静态站，Hugo 生成，推送到 GitHub Pages。没有数据库、没有后端，写文章的流程就是：

```bash
# 新建一篇文章
hugo new content posts/某个坑.md

# 本地预览（带热重载）
hugo server -D

# 推上去，GitHub Actions 自动构建发布
git add -A && git commit -m "post: 某个坑" && git push
```

文章的元信息都在文件头：

```yaml
---
title: "文章标题"
date: 2026-11-15T10:00:00+08:00
description: "列表页和搜索结果里显示的一句话摘要"
tags: ["Linux", "shell"]
---
```

## 一点排版示例

行内代码长这样：`systemctl restart chrony.service`，块引用长这样：

> 任何足够复杂的配置，都包含着一段没人记得为什么存在的历史。

表格也没问题：

| 指令 | 用途 |
| --- | --- |
| `chronyc sources` | 看当前选中的时间源 |
| `chronyc tracking` | 看本地时钟的相对偏移 |

写代码时顺手验证过的结论才敢放上来——毕竟复制一段跑不通的配置，比自己写错更浪费时间。
