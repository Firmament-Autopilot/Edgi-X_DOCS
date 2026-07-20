# 代码调试

FMT-Firmware支持通过 DAPLink、J-Link 和 OpenOCD-Infineon 进行代码单步调试，便于分析问题以及了解代码执行的过程和数据。

#### 调试环境配置

第一次使用时，需要首先配置调试环境。

1. 在你的系统上安装调试器驱动。使用 DAPLink 时，可直接使用 E83 工程自带的 `target/infineon/edge-e83/tools/OpenOCD-Infineon`；使用 J-Link 时，还需安装[J-Link Server](https://www.segger.com/downloads/jlink/)。

2. 如果使用 J-Link，创建一个新的环境变量`JLINK_SERVER`并将它的值设为J-Link Server的执行文件路径；如果使用 DAPLink，可跳过此步骤。

   **Linux/Mac**:

   ```
   export JLINK_SERVER=<JLink-Server-Path>/JLinkGDBServerCLExe
   ```

   **Windows**：

   <p align="left">
     <img src="./figures/jlink_server.png" width="50%">
   </p>

3. 在 VS Code 中安装 `cortex-debug` 插件。E83 工程的 `.vscode/launch.json` 已提供 `EDGE-E83 M33 Debug`、`EDGE-E83 M55 Debug` 和 `EDGE-E83 Multi-Core Debug` 等调试配置。

   <p align="left">
     <img src="./figures/cortex_debug.png" width="50%">
   </p>

#### 开始调试

上面我们已经配置好了调试环境，可以按照如下步骤来开始单步调试。

1. 连接 DAPLink 或 J-Link 的 SWD 引脚到飞控的 Debug 端口。连接调试器 Tx/Rx 可通过串口终端软件打开 serial0 控制台。

   <p align="left">
     <img src="./figures/jlink_connect.png" width="40%">
   </p>

2. 在BSP目录下的*rtconfig.py*文件中设置`BUILD = 'debug'`并重新编译固件。

   <p align="left">
     <img src="./figures/rtconfig_debug.png" width="40%">
   </p>

3. 在 VS Code 选择*Debug*页面，并选择对应的 E83 目标配置（例如 `EDGE-E83 M55 Debug (DAPLink)`、`EDGE-E83 M55 Debug (JLink)` 或 `EDGE-E83 Multi-Core Debug (DAPLink)`），然后点击绿色的**Run**按钮即会下载代码并开始调试。开始调试后在右上角会出现单步调试的按钮栏，可以运行、停止、单步等操作。左边*WATCH*栏可以添加要查看的变量，*CALL STACK*栏可以看到函数调用栈，*CORTEX PERIPHERALS*可以查看芯片外设的寄存器。

   <p align="left">
     <img src="./figures/jlink_debug.png" width="60%">
   </p>
