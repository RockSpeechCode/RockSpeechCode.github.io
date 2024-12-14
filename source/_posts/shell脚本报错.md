---
title: linux shell脚本报错
date: 2019-01-08 11:26:57
tags: Linux
categories: 问题记录
---
shell脚本报错 “syntax error near unexpected token `fi'”

1.cat -v mysqlmonitor.sh命令查看代码
2.linux里面查看后自动在换行符后面带上了^M的标识
3.用vim打开文件，在命令行模式下输入:set ff=unix
