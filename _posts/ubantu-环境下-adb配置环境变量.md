1、配置环境变量

进入终端，输入

```
sudo gedit ~/.bashrc
```
在文件的最后追加：

```
#set path for android sdk tools
export PATH=$PATH:/your_android-sdk-linux_path/tools/
export PATH=$PATH:/your_android-sdk-linux_path/platform-tools/
```
这里的your_android-sdk-linux_path是绝对路径

2、保存后，同步更新
```
source ~/.bashrc
```
3、如果直接运行adb之后，显示相关信息


TPM:~$ adb


> Android Debug Bridge version 1.0.31 -a - directs adb to listen on all interfaces for a connection -d - directs command to the only connected USB device returns an error if more than one USB device is present. -e - directs command to the only running emulator. returns an error if more than one emulator is running. -s- directs command to the device or emulator with the given serial number or qualifier. Overrides ANDROID_SERIAL environment variable. -p- simple product name like 'sooner', or a relative/absolute path to a product out directory like 'out/target/product/sooner'. If -p is not specified, the ANDROID_PRODUCT_OUT environment variable is used, which must be an absolute path. -H - Name of adb server host (default: localhost) -P - Port of adb server (default: 5037) devices [-l] - list all connected devices ('-l' will also list device qualifiers) connect[:] - connect to a device via TCP/IP Port 5555 is used by default if no port number is specified. disconnect [[:]] - disconnect from a TCP/IP device.
