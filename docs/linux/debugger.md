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



## OpenOCD



### 網址

[openOCD and GDB](http://openocd.org/doc/html/GDB-and-OpenOCD.html)

[openOCD for Zephyr](https://github.com/zephyrproject-rtos/openocd)

### nrf52840 board

:::image type="content" source="image/debugger/openocd_with_nrf52.png" alt-text="setup":::

- 先將openocd for nrf52 board打通, 此時會
    - 自動開通telnet server port 4444
    - 自動開通gdb server port 3333
- 選定要使用的arm gdb tool
- 

#### MISC

- 參數  
    -f : 指定config file的路徑，如果沒有設定的話，OpenOCD預設會開啟openocd.cfg
    -d : Level數字-3~3，從LOG_LVL_SILENT(-3)~LOG_LVL_DEBUG(3)
        編按: 現在多了一個LOG_LVL_DEBUG_IO(4)的Level用來底層I/O除錯用
    -l : Log file的路徑，預設會直接打印在OpenOCD的console上，加上這個可以把log導向檔案中，方便除錯!


- 快速用openocd打通的blog:

https://blog.dbrgn.ch/2020/5/16/nrf52-unprotect-flash-jlink-openocd/

    openocd -c 'interface jlink; transport select swd; source [find target/nrf52.cfg]'

- 


## pyOCD

## Debugger HW

### JTAG

### SWD

### DAPlink