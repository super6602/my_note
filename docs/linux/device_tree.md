---
id: device_tree
title: Usage of device tree
---

## device tree

### Introduction

![platform](./image/device_tree/hw_device.png)

- 一般的embedded platform: CPU + peripheral
- DT: 用來描述hardware的語言

![platform](./image/device_tree/hw_diccoverable.png)

### discoverability

- 事先不用預知裝置的peripheral
- 可以透過讀vendor id/chip id去enum device
- 像是PCIE和USB
- I2C/Uart/SPI就沒有discoverability, 亦即需要事先config, 而非dynamically discoverible

![platform](./image/device_tree/hw_description_for_non_diccoverable.png)

- 利用device描述語言讓OS/bootloader知道HW資訊(board level information)
- 

![platform](./image/device_tree/hw_description_for_non_diccoverable2.png)

- ARM/x86: 使用ACPI描述non-discoverable的裝置
- Linux: 使用device tree描述硬體裝置, 現今porting Device Tree在linux上很重要


### device tree source

![platform](./image/device_tree/dts.png)

- 描述語言稱為device tree source (.dts)
- 經過compile (dtc)後生成dtb(blob)
- DTB可以
    - 直接被bootloader linked
    - Ex: 現在如果在arm linux platform boot, 會
        - boot from u-boot bootloader
        - use bootset to takes the kernel address (where the kernel image has been loaded into ram and device tree blob address) so the OS are able to access the DTB and able to recognize what hardware is abailable and how it is organized

#### syntax

![platform](./image/device_tree/dts_syntax.png)

- tree of nodes
- property_value: 可以為int or array or string etc.
- node用來描述one device or IP block
- properties用來描述device characeristics


#### example

![platform](./image/device_tree/dts_example.png)

- compatible, address-cells, size-cells
- 
- 

![platform](./image/device_tree/dts_example2.png)

- CPU0: 用來做label; 可以被其他node properties reference到 (使用phandle)

![platform](./image/device_tree/dts_example3.png)

- 只有一個property: device_tpye = "memory"
- reg: base address + size
- chosen: 讓fw or bootloader能夠傳遞extra information給OS (which is not exactly HW description, 像是傳遞kernel command line)

![platform](./image/device_tree/dts_example4.png)

- soc: 比較重要的部分; name會因platform而有所差異, is not specified by DTS

![platform](./image/device_tree/dts_example5.png)

- DT mimics the topoligy of the hardware
- Ex: eeprom: 一個連接在I2C上的eeprom, 所以排在i2c的sub-node

![platform](./image/device_tree/dts_example6.png)

- USB內沒有subnode, 因為是dynamically discoverible, 就不用再特別描述


#### DTS inheritance


![platform](./image/device_tree/dts_locate.png)

- 還沒有制式保存DTS的地方

![platform](./image/device_tree/dts_inheritance.png)

- DTS file被非monolithic (可以被拆分成許多檔案, 讓他可以被重複使用)
- dtsi(include): include file for other DTS file; DTC只能吃DTS檔案, 但DTS可以include任何的DTSI
- DTSI contains the definition of what is inside the system on chip; 再讓所有的DTS board definition去include DTSI
- DTS 內容為贏過#include DTSI的內容
- 用#include

##### example

![platform](./image/device_tree/dts_inheritance_example.png)

- 右邊的DTS會覆蓋DTSI相同的設定;
- 右邊的DTS會加入DTSI沒有的設定
- 這只是描述compiled後DTS會有的長相, 事實上編出來會是DTB file

![platform](./image/device_tree/dts_inheritance_example2.png)

- 直接使用@uart0做phandle覆蓋是linux目前prefered的做法

### Building Device Tree in Linux

![platform](./image/device_tree/dt_build.png)

- 使用DTC就能邊DT

![platform](./image/device_tree/dt_build2.png)

- DTC只會做語法檢查
- YAML可以做語義上的檢查
- 利用dt_bindings_check和dtbs_check
- 協助檢查一些typo的錯誤

### Exploring the DT on the target

![platform](./image/device_tree/dt_explore.png)

- 在linux上可以run time用DTC去檢查現在使用的device tree

### Run time modify

![platform](./image/device_tree/dt_run_time_modify.png)

- 可以動態改DT
- 但實際上只有很少的部分可以被修改
- 用一些command在u-boot上操作
