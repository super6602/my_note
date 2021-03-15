---
id: zephyr
title: Usage of zephyr
---


## MISC


[connect over IP](https://github.com/project-chip/connectedhomeip#connected-home-over-ip)

[ninja](https://blog.csdn.net/yujiawang/article/details/72627121)

[NCS教學](https://www.eet-china.com/mp/a34779.html )

[NCS SDK get started](https://devzone.nordicsemi.com/nordic/nrf-connect-sdk-guides/)

## Overview

### Zephyr Overview

![platform](./image/zephyr/zephyr_ecosystem.png)

![platform](./image/zephyr/zephyr_repo.png)

- main 是和3rd-party比較無關的部分
- 利用west maintain repos

![platform](./image/zephyr/zephyr_build.png)

- Koncfig: 用python重新實作以達到cross platform
- Device Tree: 有點不同於linux中的device tree, HW都是在build time決定, 沒有run time HW detection

![platform](./image/zephyr/zephyr_arch.png)


![platform](./image/zephyr/zephyr_kernel.png)

![platform](./image/zephyr/native_ip_stack.png)


![platform](./image/zephyr/bt_host_and_mesh.png)

- 多種HCI INF的選擇


![platform](./image/zephyr/bt_controller.png)

- 下一世代會區分upper/lower link layer
- le audio正在做

![platform](./image/zephyr/zephyr_usb.png)

![platform](./image/zephyr/native_exe_on_posix.png)

- 把zephyr當成Linux app的一部分去debug, 可使用linux原生的debug tool


### West Overview

![platform](./image/zephyr/west.png)

- stand alone CLI app for managing workspace
- 也提供了west extension

![platform](./image/zephyr/west2.png)

- build in
  - 用來config和sync code from repo
- extension
  - 用來Build/flash/debug code

![platform](./image/zephyr/west_workspace.png)

- west.yaml為manifest file, 決定了哪些git repo為current working repos

![platform](./image/zephyr/west_workspace_manage.png)

- modules會根據west.yaml的設定去clone相對應的git repo下來
- modules為3rd-party code

![platform](./image/zephyr/west_example.png)

- west.yaml決定commit和path

![platform](./image/zephyr/west_example2.png)

![platform](./image/zephyr/west_example3.png)

- 可以用guiconfig去編輯Kconfig的內容

![platform](./image/zephyr/west_example4.png)

- 使用zephyr shell, 用COM12溝通並mount fs

![platform](./image/zephyr/west_example5.png)

- 使用west debug, 會重新Build並且開出gdb

![platform](./image/zephyr/west_example6.png)

- west attach, 和debug很像, 除了不會refresh anything
- 他直接drop directly into a gdb prompt which is running on the target

![platform](./image/zephyr/west_example7.png)

#### MISC

[west config](https://blog.csdn.net/bruceoxl/article/details/109139821)

#### Parameters

#mkdir build && cd build
#cmake -GNinja -DBOARD=
#ninja
3.1.2 menuconfig

 使用west：
#west build -t menuconfig
 ninja：
#ninja menuconfig
