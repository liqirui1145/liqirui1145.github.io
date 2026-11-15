---
title: "自包含安装器：三个让人翻车的 shell 细节"
slug: "shell-installer-pitfalls"
date: 2026-11-05T14:00:00+08:00
description: "把脚本打包成单个 .run 交付时，set -u、pipefail 和载荷里的输出函数各埋了一次雷。"
tags: ["shell", "Linux"]
---

把一堆脚本、systemd 单元、配置模板塞进一个 `.run` 文件里发给别人，是个很舒服的交付方式：不依赖网络、不依赖包管理器，`chmod +x` 就能跑。但也正因为"自包含"，很多平时不显眼的问题会被放大。

## 一、`set -u` 和变量名不一致

`set -u` 会在引用未定义变量时立刻退出。它很有用，直到你把 `$CONF_FILE` 写成 `$CONF_PATH`：

```bash
#!/usr/bin/env bash
set -Eeuo pipefail

CONF_FILE="/etc/something.conf"
echo "写入 $CONF_PATH"   # 未定义 → 直接退出
```

麻烦的是这类错误往往只在某一条分支上触发。之前只测了 `extract` 分支，安装路径里负责打印 banner 的那段带着笔误，一装就崩。

**对策**：把每个入口都跑一遍——`help`、`version`、`extract`、`install`，以及安装流程里每个会打印信息的子命令。只测主路径不够。

## 二、`pipefail` 把"显示用"的命令变成致命错误

```bash
VERSION="$("$SOME_TOOL" --version | sed -n 's/.*v\([0-9.]*\).*/\1/p')"
```

如果 `$SOME_TOOL` 压根不存在，管道返回 127，`pipefail` + `errexit` 会让整个脚本以一个莫名其妙的错误码中断——而这段代码只是想在 banner 里显示个版本号。

**对策**：纯装饰性的调用要么加 `|| true`，要么先判存在：

```bash
if [[ -x "$SOME_TOOL" ]]; then
  VERSION="$("$SOME_TOOL" --version | sed -n 's/.*v\([0-9.]*\).*/\1/p')"
else
  VERSION="unknown"
fi
```

## 三、载荷脚本是独立文件，`say` 不会跟过去

主安装器里通常有一组输出函数：

```bash
say()  { printf '%s\n' "$*"; }
info() { printf '  %s\n' "$*"; }
ok()   { printf '  \033[32m✓\033[0m %s\n' "$*"; }
```

但安装器释放出来的 `mycmd` 管理脚本是**另一个文件**，它执行时主脚本早就退出了，这些函数一个都不存在。漏定义的情况下：

```text
mycmd status
/usr/local/bin/mycmd: 行 12: say: 未找到命令
```

更麻烦的是，这种失败**不产生非零退出码**（命令找不到只在那一行报错，脚本继续往下跑），所以"退出码为 0"这种校验完全抓不到。

**对策**：

- 载荷脚本里自己定义一份 helper，别指望继承。
- 测试时除了看退出码，还要扫输出：

```bash
out="$(./installer.sh extract "$tmpdir" 2>&1)"
[[ $out == *"command not found"* || $out == *"未找到命令"* ]] && fail "载荷缺少 helper"
[[ $out == *"unbound variable"* ]] && fail "存在未定义变量"
```

## 小结

自包含交付的优势是"拿来就能跑"，代价是**所有依赖都得自己兜住**。写完的检查清单只有三条：

1. 每个入口都走一遍，不只看退出码；
2. 装饰性命令不要参与错误传播；
3. 载荷里被调用的每个函数，都确认在载荷内定义过。
