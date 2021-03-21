---
id: arm_coresight
title: Usage of arm_coresight
---

## MISC

[ArmCoreSight](https://www.youtube.com/watch?v=dVBJC51XPts&list=WL&index=1&t=701s)

## Introduction

![platform](./image/arm_coresight/arm_coresight.png)

- Arm裡面的debug IP

### level1 Real-Time Debug

![platform](./image/arm_coresight/level1.png)


- 使用JTAG(20 pin)/SWD(2 pin) interface
- 可以做到
    - control step work
    - break point
    - memory access unit

### level2 - Data Trace

![platform](./image/arm_coresight/level2.png)

- Data Trace
    - 分為DWT和ITM模組
    - 紀錄PC, event counters, 和interrupt的timing statictics (output to SWD, SWDCLK)
    - Trace Data使用printf來debug (output to SWO)

#### ITM

![platform](./image/arm_coresight/ITM.png)

- ITM模組是ARM選配的
- 使用SWO pin output
- ITM channel 0/31式預設頻道
- ITM 1~30是user define


### level3 - Instruction Trace(ETM)

![platform](./image/arm_coresight/ETM.png)

- 2 pins debug (TMS, TCK)
- 4 pins for Trace (TRACEDATA[3~0])
- 可以解析所有的assembly code, 和C/C++ code的執行時間
- 也可以看if/else的executed
- 能夠仔細分析所有application function的執行佔比

## Keil Usage

### Level1

![platform](./image/arm_coresight/keil_level1.png)

- 一般的register/peripheral/memory access control
- step in/out控制
- 斷點設置

### Level2

![platform](./image/arm_coresight/keil2_trace_enable.png)

- 把Trace Enable

#### printf view


![platform](./image/arm_coresight/debug_viewer.png)


![platform](./image/arm_coresight/LA_enable.png)

- 可以將variable輸出至LA上面顯示

![platform](./image/arm_coresight/LA_probe.png)

- 結果如上圖

![platform](./image/arm_coresight/thread_debug.png)

![platform](./image/arm_coresight/thread_debug2.png)

- 把thread的資訊也吐出來

![platform](./image/arm_coresight/trace_exception.png)

![platform](./image/arm_coresight/trace_exception2.png)

- 看exception的次數和執行時間

### Level3

![platform](./image/arm_coresight/ETM_enable.png)

- 選擇ETM enable

![platform](./image/arm_coresight/ETM_enable.png)

- 可以看到所有assembly/c的執行時間和time_stamp


![platform](./image/arm_coresight/code_coverage.png)

- 看所以function的coverage



![platform](./image/arm_coresight/performance_analyzer.png)

- 看所有function的佔比


## Event Recorder