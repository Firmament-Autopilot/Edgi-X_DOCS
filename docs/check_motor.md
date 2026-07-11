# 检查电机

首先，检查电机的安装顺序和旋转方向非常重要，因为错误安装电机可能会导致飞行器受损。要验证电机的安装顺序和方向，请参考[载具/机架](http://localhost:3000/#/content_ch/introduction/frameref)部分。

要验证电机的安装情况，您可以使用 act 命令逐个驱动每个电机。

> 注意：检查电机前请务必卸下桨叶以确保安全。

首先禁用控制器的输出，以防止其覆盖 act 指令的输出。

```
mcn suspend control_output
```

要选择电机通道，您可以使用 `-c` 或 `--channel` 选项，后面跟着一个表示通道位掩码的十六进制值。例如，使用以下命令可以驱动或停止电机1。将 "1" 替换为 "2" 可以控制电机2，"4" 控制电机3，"8" 控制电机4，依此类推。

例如如下指令将1号电机输出设置为1150（慢速旋转），然后再设置为1000（停转）。依次测试各个电机，确认电机的序号，旋转方向和转速都正常。

```
act set -d main_out -c 1 1150
act set -d main_out -c 1 1000
```

经测试过后，若电机运转正常，你可以恢复控制器的输出。

```
mcn resume control_output
```

为了简化这个过程，您可以将所有这些命令整合到一个名为 `check_motor.sh` 的脚本中，如下所示：

```
mcn suspend control_output
act set -d main_out -c 1 1150
delay 3000
act set -d main_out -c 1 1000
delay 1000
act set -d main_out -c 2 1150
delay 3000
act set -d main_out -c 2 1000
delay 1000
act set -d main_out -c 4 1150
delay 3000
act set -d main_out -c 4 1000
delay 1000
act set -d main_out -c 8 1150
delay 3000
act set -d main_out -c 8 1000
delay 1000
mcn resume control_output
```

然后通过执行以下命令来运行这个脚本

```
exec check_motor.sh
```