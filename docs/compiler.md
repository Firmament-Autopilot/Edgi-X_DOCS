# 代码编译

在编译固件之前，理解 FMT-Firmware 的目录结构非常重要。以下是您在 FMT-Firmware 项目中通常可以找到的典型目录结构的概述：

<p align="center">
  <img src="./figures/fmt_dir_structure.png" width="30%">
</p>

目录文件解释：

1. **src/**:这个目录包含固件的主要源代码文件。通常情况下， `startup.c` 文件是固件的入口点。其他目录，例如 `driver`、`hal`、`lib`、`modules` 等，表示固件的不同组件，使其模块化且具有良好的组织性。
2. **rtos/**:在这个目录中，您会找到与 FMT-Firmware 项目中使用的实时操作系统 (RTOS) 相关的代码。具体来说，这个目录包含了RTOS的实现，本项目使用的是 [rt-thread](https://www.rt-thread.io/) RTOS。
3. **target/**:这个文件夹包含了针对固件目标硬件的板级支持包 (BSP)。BSP 是一个关键组件，提供了各硬件平台的外设驱动支持，以让FMT可以在上面运行。
4. **unit_test/**:`unit_test/` 目录包含单元测试的代码，单元测试在固件中起着至关重要的作用，它可以单独测试每个组件或单元，确保其健壮性和正确性。单元测试对于保持固件的可靠性至关重要。

-------------------------------

要了解如何构建和下载固件，可查阅每个 BSP 的 README 文件。README 文件通常提供了详细的步骤、依赖项和配置说明，确保固件成功编译和下载。

在以下说明介绍中，我们将以 编译**SIESON S1**飞控作为示例，来阐述固件构建过程。

1. 使用系统终端导航到BSP所在目录：

   ```
   cd FMT-Firmware/taget/sieon/s1
   ```

2. 使用scons命令启动构建过程：

   ```
   scons -j4
   ```

   > 使用 `-j4` 标志可以启用并行构建，使用 4 个任务同时进行，加快构建过程并减少编译时间。

   默认情况下，固件是编译X型四旋翼飞行器，等同于执行`scons -j4 --vehicle=Multicopter --airframe=1`，若要编译其他载具或机架类型固件，可参考FMT文档有关[载具/机架](https://docs.sieon.net/fmt/#/content_ch/introduction/frameref)部分的说明。

3. 编译完成后，可在BSP的build目录下看到固件的生成文件。若出现编译错误，可先尝试使用`scons -c`清理下目录，再重新编译。

   ```
   PS D:\ws\FMT\FMT-Firmware\target\sieon\s1> scons -j8
   scons: Reading SConscript files ...
   b''
   scons: done reading SConscript files.
   scons: Building targets ...
   scons: building associated VariantDir targets: build
   CC build\board\board.o
   CC build\board\bsp_cmd.o
   ......
   LINK build\fmt_sieon-s1.elf
   Memory region         Used Size  Region Size  %age Used
               CODE:      817624 B      1920 KB     41.59%
           ITCM_RAM:          0 GB        64 KB      0.00%
           DTCM_RAM:          0 GB       128 KB      0.00%
             SRAMD1:      240724 B       512 KB     45.91%
             SRAMD2:          0 GB       256 KB      0.00%
             SRAMD3:          0 GB        64 KB      0.00%
             BKPRAM:          0 GB         4 KB      0.00%
    RxDecripSection:          96 B        32 KB      0.29%
    TxDecripSection:          96 B        32 KB      0.29%
     RxArraySection:        6112 B        32 KB     18.65%
   arm-none-eabi-objcopy -O binary build\fmt_sieon-s1.elf build/fmt_sieon-s1.bin
   arm-none-eabi-size build\fmt_sieon-s1.elf
      text    data     bss     dec     hex filename
    817624   46544  200484 1064652  103ecc build\fmt_sieon-s1.elf
   scons: done building targets.
   ```

   
