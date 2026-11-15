---
title: "chronyd 读不到配置：AppArmor 挖的坑"
slug: "chrony-apparmor"
date: 2026-11-10T21:30:00+08:00
description: "拿 chronyd -Q 校验一份写在 /tmp 的配置，结果只有一句 Permission denied。问题不在权限位，在 AppArmor。"
tags: ["Linux", "chrony", "AppArmor"]
---

改 chrony 配置时最稳的流程是：先拿 `chronyd -Q -f` 试跑一次，确认语法没问题再重启服务。某次我照惯例把草稿写进 `/tmp`，结果直接被打回：

```text
Could not open /tmp/chrony-test.conf : Permission denied
```

文件是 `644`、属主是我自己、用 `cat` 能正常读。所以问题不在文件权限。

## 原因

Ubuntu 给 chronyd 装了 AppArmor 策略（`/etc/apparmor.d/usr.sbin.chronyd`），只放行有限的几个目录：

- `/etc/chrony/**`
- `/var/lib/chrony`
- `/var/log/chrony`

`/tmp` 不在白名单里。这不是 DAC 权限，是 LSM 层面直接拒绝，所以 `ls -l` 看起来一切正常也没用。

## 验证

```bash
sudo aa-status | grep -i chrony
```

```text
usr.sbin.chronyd
```

确认策略处于 enforce 状态后，把配置复制到允许的目录再校验：

```bash
sudo cp /tmp/chrony-test.conf /etc/chrony/chrony-test.conf
sudo chronyd -Q -f /etc/chrony/chrony-test.conf
```

这次就能正常解析了，有语法错误也会明确指出是第几行。

## 顺带一个坑

`minpoll` / `maxpoll` 在 chrony 里**只能作为 `server` 行的选项**，没有全局指令。把它们单独写在文件顶层，chronyd 会直接启动失败——而这个失败在 AppArmor 那层是看不出来的，因为配置根本没被读到。

所以排查顺序应该是：

1. `aa-status` 确认策略状态
2. 配置文件是否在允许路径
3. 再谈配置内容对不对

## 小结

`Permission denied` 不一定是权限问题。在启用 AppArmor / SELinux 的系统上，先确认是不是 LSM 在拦，能省掉不少"`chmod 777` 试试"的时间。
