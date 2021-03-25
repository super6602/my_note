---
id: bare_metal_startup
title: arm cortex M based startup
---

## MISC

[youtube for arm startup](https://www.youtube.com/watch?v=qWqlkCLmZoE&list=PLERTijJOmYrDiiWd10iRHY0VRHdJwUH4g&index=1)

[download method](https://blog.csdn.net/m0_37621078/article/details/106798909)

[flash loader](https://my.oschina.net/u/4318517/blog/3299813)

## Build Process

### Cross Compliation and Toolchains

- 跨平台可以compile, assemble, 和link的binaries
- 提供target的debug binaries
- 目前ARM有提供兩種tool-chains
    - GNU for ARM embedded Processors (免費的)
    - ARM-CC: arm提供的, ships with KEIL, 需要licensing

以下為ARM-GCC的重要binaries

![platform](./image/bare_metal_startup/arm-gcc.png)

- arm-none-eabi-gcc: 不只compile, 還有assemble & link obj並且創造executable file
- arm-none-eabi-ld: 顯性呼叫linker
- arm-none-eabi-as: 將assembly code轉成machine code實際的執行檔

### Build

![platform](./image/bare_metal_startup/build_process.png)

1. 經過preprocessor將macro展開/將conditional compile展開, 並將.c檔轉換成.i檔
2. 經過code generator產生.s file(assembly language), 這個過程稱為mnmeonics
3. 經過assembler產生.o file (relocatable object file), 此時還沒有絕對定址, 此時帶有的是relocatable address

- 以上這三步可藉由arm-none-eabi-gcc完成

![platform](./image/bare_metal_startup/linker-stage.png)

4. 所有的.o檔都會藉由linker, 將所有的.o檔的section relocate, solve symbols, 並生成.elf file
5. 經過obj-copy, 將bin檔案dump出來

#### example build

![platform](./image/bare_metal_startup/arm-gcc-example.png)


- -c: just compile/assemble, 但是不要link
- -S: just compile, 輸出assembly file
- 上面代表assembler失敗, 因為沒有指定processed architecture (M0 or M4, etc..)
  

![platform](./image/bare_metal_startup/option-march.png)

- -march: 選擇arm architecture; cortex-M4是選擇armv7ve


![platform](./image/bare_metal_startup/option-march.png)

- -mcpu(same as mtune): 如果不知到用啥架構, 就可以使用該option

![platform](./image/bare_metal_startup/option-thumb.png)

- mthumb: cortex M系列只支援thumb state

### Relocatable Object file

![platform](./image/bare_metal_startup/relocatable_file.png)


- .o檔為elf format
- ELF為GCC標準的 object files and executable files
- ELF描述了data/rodata/code/...的section的描述
- COFF為Unix System用的
- 如果用的是ARM-CC, 則會使用AFI format

#### example of objdump of .o file

![platform](./image/bare_metal_startup/example_objdump.png)


![platform](./image/bare_metal_startup/example_objdump2.png)

- 可以發現dump .o檔案後的address都非絕對位址, 僅僅只是相對位址
- 再用linker scrip relocate MCU start address

## Startup File

![platform](./image/bare_metal_startup/startup_file.png)

- startup code -> main
- startup code為target dependent, 
    - 會包含vector table placement in code memory
    - initialize SP, etc...
    - turn some HW unit (Ex: FPU)
    - 初始化.data/.bss in SRAM

### basic startup file

基本要實現的3個ARM startup file

1. init vector table
2. init .data/.bss
3. jump into main()

#### vector table

![platform](./image/bare_metal_startup/vector_table.png)

- vector table描述了各interrupt handle時需要jump的memory

![platform](./image/bare_metal_startup/vector_table2.png)

- vector table包含了system exception (for ARM CPU usage)和N個IRQs (根據CPU型號有所不同)

#### example vector table

![platform](./image/bare_metal_startup/example_startup.png)

- 利用attribute將vector table放置於.isr_vector section

![platform](./image/bare_metal_startup/example_startup2.png)

- 可以看到多了一個.isr_vector section

![platform](./image/bare_metal_startup/example_startup3.png)

- 多製造一個NMI_Handler
- 沒必要每個exception都處理 -> 使用alias解決

![platform](./image/bare_metal_startup/alias.png)

- weak: 給其他有定義的function來覆寫的
- alias: 讓function定義其他的function name

![platform](./image/bare_metal_startup/example_alias.png)

- 讓Default_Handler作為NMI_Handler的別名
- 所以NMI exception發生時, 就會跑到Default_Handler處理

![platform](./image/bare_metal_startup/example_weak.png)

- 使用weak, 讓其他層去implement各exception的handle

![platform](./image/bare_metal_startup/data_section_boundary.png)

- 需要把存在flash的.data section copy到SRAM中, 此時就要知道.data section的boundary
- linker中會export _edata/_sdata/_etext等資訊

## Linker Scrips

![platform](./image/bare_metal_startup/linker_scrip.png)

- 讓不同的obj檔案merge為輸出檔
- assign絕對位址
- -T option

常用的commands

- ENTRY
- MEMORY
- SECTIONS
- KEEP
- ALIGN
- AT>

### Linker Commands

#### ENTRY

![platform](./image/bare_metal_startup/linker_entry.png)

- 將Reset_Handler設置在ENTRY point
- debugger將這個資訊放在first function to execute


#### MEMORY

![platform](./image/bare_metal_startup/linker_memory.png)

- 一個linker scrip只能有一個MEMORY command


![platform](./image/bare_metal_startup/memory_command.png)

![platform](./image/bare_metal_startup/memory_attribute.png)

- attribute: 描述section的性質

![platform](./image/bare_metal_startup/example_linker_scrip.png)

#### SECTIONS

![platform](./image/bare_metal_startup/linker_merge.png)

- 每個.o file都會有各自的.text/.data/.bss/.rodata section, 需要用SECTIONS描述這些區段的address/memory

![platform](./image/bare_metal_startup/section_map.png)

- 根據flash規劃的memory map, 撰寫SECTION

![platform](./image/bare_metal_startup/section_syntex.png)

- .text: 最後輸出的section; {} 裡面則是所有的input section
- VMA: virtual memory address, 
- LMA: load memory address
- 將區段存放(LMA)和最後存取(VMA)的描述

![platform](./image/bare_metal_startup/example_linker_scrip2.png)


### Location Counter


![platform](./image/bare_metal_startup/location_counter.png)

- .
- linker會動更新目前分配到哪個address
- 只能在SECTION command內使用

![platform](./image/bare_metal_startup/example_linker_scrip3.png)

- 利用location counter指定區段的開頭/結尾, 給startup code使用

![platform](./image/bare_metal_startup/data_section_boundary.png)

- 在startup code裡面去copy .data區段

### Linker Map

![platform](./image/bare_metal_startup/example_make_file.png)

- -Map: 產生memory map
- -T: 指定linker scrip
- 這邊使用arm-none-eabi-gcc(compile, 但也包含linker driver) command來使用linker (-T, -Map); 加入-Wl,表示後面是linker argument


![platform](./image/bare_metal_startup/example_linker_map.png)

## Flashing and Debugging

![platform](./image/bare_metal_startup/flash_download.png)

- 利用adaptor連上CPU, 將host call轉換成SWD/JTAG debug protocol
- 在host使用openOCD/JlinkExe, etc.下command以控制target

### OpenOCD

![platform](./image/bare_metal_startup/openocd.png)

- 使用openOCD download and debug using GDB


### Remote Debug

![platform](./image/bare_metal_startup/flash_download.png)

1. OpenOCD用ST-LINK driver透過USB和adapter連線 (openOCD sends USB packets and transfer to SWD)
2. Adaptor利用SWD（2-pin） or JTAG和Target連線
3. DP (debug access port): arm內部用來和processor溝通的module; 利用DP可以存取許多的bus interfaces (AHB-AP/APB-AP)
4. AHB-AP(access point): 再透過該bus controller向Flash controller操作, 
5. Core: 可以設置BP, halt, etc...

![platform](./image/bare_metal_startup/step_download_using_openocd.png)

#### example

1. run openOCD server, 此時會locate GDB server on 3333


![platform](./image/bare_metal_startup/example_openocd2.png)

2. 打開arm-none-eabi-gdb.exe, 去init GDB client
3. monitor: 用來區分是GDB command or native openOCD command; 連上talnet時就不用打monitor

![platform](./image/bare_metal_startup/example_openocd3.png)

4. 使用flash write_image寫入.elf file


## 'C' standard library

### Newlib

![platform](./image/bare_metal_startup/newlib.png)

![platform](./image/bare_metal_startup/newlib-nano.png)

![platform](./image/bare_metal_startup/newlib_location.png)

- spec file: use during linker stage to make your application link to newlib/newlib-nano

### Low Level System Calls

![platform](./image/bare_metal_startup/low_level_system_call.png)

- Low level system call: 操作實際硬體的部份
- Newlib: 實現standard C library中HW相關的部份; 不提供low level system call的實作

![platform](./image/bare_metal_startup/example_low_level_system_call.png)

- printf: standard c library function
- Newlib-Nano: 提供'C' standard library
- _write(): low level system calls, 操作UART/ITM/LCD, etc...
- newlib redirect printf to low-level write function

![platform](./image/bare_metal_startup/example_low_level_system_call2.png)

- syscall.s: contains the function definition of some of important system calls, such as _write/_read/etc...; this file does not contain complete implementation of all system calls (implementation system call depends on your target), this file only provides function definition without build errors

#### example

![platform](./image/bare_metal_startup/example_system_call.png)

- --specs=nono.spec: link c standard library with newlib-nano

![platform](./image/bare_metal_startup/example_system_call2.png)

- 發現有symbol不認識; 需要在linker裡面加

![platform](./image/bare_metal_startup/example_system_call3.png)

- 加入symbol


![platform](./image/bare_metal_startup/example_system_call4.png)

- 使用semihost

![platform](./image/bare_metal_startup/example_system_call5.png)

- 在server打開semihost
