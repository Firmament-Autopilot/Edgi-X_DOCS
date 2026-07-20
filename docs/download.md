# 固件下载

在编译好固件后，我们需要将编译好的固件下载到飞控硬件中。FMT当前支持**3种下载方式**。

## E83 烧写顺序

Edge-E83 固件由 bootloader、M33 和 M55 三部分组成。首次烧写、完整更新或修改 bootloader/M33 相关配置后，请按 **bootloader -> M33 -> M55** 的顺序烧写：

```
cd FMT-Firmware/target/infineon/edge-e83/tools

.\m33_program.ps1 -hex_file ..\bootloader\build\sign_rtthread.hex
.\m33_program.ps1 -hex_file ..\m33\build\rtthread.hex
.\m55_program.ps1 -hex_file ..\m55\build\fmt_e83-m55.hex
```

其中 bootloader 负责安全启动、镜像校验和应用加载；M33 负责系统管理、底层服务和跨核协同；M55 运行 FMT 飞控主固件和控制算法。若只修改飞控应用或模型算法，通常只需重新编译并烧写 M55；若修改 bootloader 配置，建议先在 `bootloader` 目录执行 `scons -c`，再按 bootloader、M33、M55 的顺序重新编译和烧写。


1. **下载脚本**: 在终端中进入 E83 BSP 目录并输入`python .\uploader.py --firmware build/fmt_e83-m55.hex`，然后使用USB连接飞控硬件到电脑。脚本将自动识别到飞控端口，并开始下载。

   > 注意，需要使用python 3，请确保python 3有正常安装。

   ```
   PS D:\ws\FMT\FMT-Firmware\target\infineon\edge-e83\m55> python.exe .\uploader.py --firmware build/fmt_e83-m55.hex
   waiting for the bootloader...
   wait for connect fmt-fmu...
   Using port COM12 : USB Serial Device (COM12)
   Attempting reboot on COM12 with baudrate=57600...
   If the board does not respond, unplug and re-plug the USB connector.
   
   Found board id: Edge-E83 bootloader on COM12
   target: Edge-E83
   firmware: build/fmt_e83-m55.hex
   Windowed mode: False
   
   Erase  : [====================] 100.0%
   Program: [====================] 100.0%
   Verify : [====================] 100.0%
   Rebooting. Elapsed Time 14.638
   ```

   > 如果出现 `"ModuleNotFoundError: No module named 'serial'"`错误, 说明缺少 **pyserial** 组件, 输入`pip3 install pyserial` 进行安装.

2. **QGC地面站下载**: 点击*载具设置*的`固件`界面，然后使用USB将飞控连接到电脑。在弹出的对话框中选择 *高级设置->自定义固件文件* ，然后选择 E83 的固件文件(`fmt_e83-m55.hex`)进行下载。

   <p align="center">
     <img src="./figures/qgc_download.png" width="70%">
   </p>

3. **调试器下载**: 如果你有 DAPLink 或 J-Link，可以将其连接到硬件的DEBUG端口来下载固件。E83 工程提供了基于 OpenOCD-Infineon 的 VS Code 调试配置，也可以使用 `target/infineon/edge-e83/tools` 下的脚本进行下载。关于如何使用和配置调试器，请参考[调试](debug.md)章节的内容。

   > 使用调试器下载时请小心不要覆盖掉 bootloader 区域。

   <p align="center">
     <img src="https://www.segger.cn/fileadmin/images/products/J-Link/J-Link_Edu/SEGGER_J-Link-EDU_right.png" width="25%">
   </p>

当系统启动并运行时，系统横幅可以通过 `serial0`（串行控制台）显示，或者可以在 QGroundControl（QGC）的 mavlink 控制台中输入 `boot_log` 来查看。这些信息提供了关于系统状态和配置的重要信息，帮助用户监控系统行为，确保系统正常运行。

```
   _____                               __ 
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/ 
Firmware.....................FMT FW v1.1.0
Kernel....................RT-Thread v4.0.3
RAM................................1408 KB
Target............................Edge-E83
Vehicle........................Multicopter
Airframe.................................1
INS Model....................CF INS v1.0.0
FMS Model....................MC FMS v1.0.0
Control Model.........MC Controller v1.0.0
Task Initialize:
  mavobc................................OK
  mavgcs................................OK
  logger................................OK
  status................................OK
  vehicle...............................OK

```

