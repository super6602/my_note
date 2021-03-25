---
id: debugger
title: debugger knowledge
---

## GBD

### introduction

硬體需求： 兩台機器

- 一台為調適的linux (embedded linux）, 一台為執行gdb的client
- ggb為clinet-server架構, 被除錯端為gdb server

Note

    gdb-server不一定是"gdb", 只要實現gdb通訊協定就好

client-server可以為

- TCP/IP
- JTAG
- COMport

Linux kernel中內建kgdb, kdb(在linux kermel內, 但沒有使用到linux kernel的功能), 在linux當機時仍能運作

- 編kernel 計得把kgdb和kdb編進去
- linux debug 時, linux內的kgdb + kdb為gdb-sever
- kgdb和kgb通過硬體將資料傳出 （Uart, WIFI, etc...）

### MISC

- cgdb: colord gdb
- tui: 可以show出layout

