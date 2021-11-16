nanoESP32-S3
-----------
[中文](./README_cn.md) [English](./README.md)

* [nanoESP32-S3介绍](#nanoESP32-S3介绍) 
* [模组规格](#模组规格)
* [ESPLink](#ESPLink)
* [产品链接](#产品链接)
* [参考](#参考)


# nanoESP32-S3介绍
nanoESP32-S3 是MuseLab基于乐鑫ESP32-S3-WROOM-1-N8模组推出的开发板，板载USB转串口，TYPE-C、全彩LED，板载的下载器支持USB CDC串口，拖拽烧录和jtag调试，方便客户进行快速的原型验证及开发。  

  
<div align=center>
<img src="https://github.com/wuxx/nanoESP32-S3/blob/master/doc/nanoESP32-S3-1.jpg" width = "4000" alt="" align=center />
</div>

# 模组规格 
nanoESP32-S3 开发板使用乐鑫官方模组ESP32-S3-WROOM-1-N8, 以下为具体规格参数

Component|Detail |
----|----|
MCU          | 32bit Xtensa LX7 dual core up to 240MHz |
ROM          | 384KB |
SRAM         | 512KB |
Flash        | 8MB |
WiFi         | IEEE 802.11bgn |
Bluetooth    | BLE 5.0 & mesh |
GPIO         | 45 |
SPI          | 4 |
UART         | 3 |
I2C          | 2 |
I2S          | 2 |
RMT          | 1T+1R |
DMA          | 3T+3R |
LED-PWMC     | 8 channel |
TWAI         | 1 |
ADC          | 2 x 12bit, 20 channel |
Temp sensor  | 1 |
Touch sensor | 14 |
Timer        | 8 |
USB-OTG      | 1 |
LCD          | 1 |
DVP          | 1 8-16bit |
Security     | OTP/AES/SHA/RSA/RNG/HMAC |


# ESPLink
nanoESP32-S3 板载一个称之为ESPLink的下载器，支持USB转串口、拖拽烧录、JTAG调试，以下是详细的说明
## USB-to-Serial
和传统的使用方式兼容（替代CP2102、CH340之类的USB转串口芯片），可使用esptool.py进行烧录或串口调试，举例如下：  
```
$idf.py -p /dev/ttyACM0 flash monitor
$esptool.py --chip esp32s3 \
           -p /dev/ttyACM0 \
           -b 115200 \
           --before=default_reset \
           --after=hard_reset \
           --no-stub \
           write_flash \
           --flash_mode dio \
           --flash_freq 80m \
           --flash_size 2MB \
           0x0     esp32s3/bootloader.bin \
           0x8000  esp32s3/partition-table.bin \
           0x10000 esp32s3/blink_100.bin
```
  
## 拖拽烧录
板载的下载器ESPLink支持拖拽烧录，将开发板上电之后，PC将会出现一个名为`ESPLink`的虚拟U盘，此时只需将flash镜像文件拖拽至`ESPLink`虚拟U盘中，稍等片刻，即可自动完成烧录。此功能使得烧录无需依赖任何外部工具以及操作系统，
典型的使用场景列举如下：快速的原型验证、在云端服务器进行编译、然后在任意PC上烧录、或者在商业的产品中实现固件的快速升级。  
注意：flash镜像文件为三个文件(bootloader.bin/partition-table.bin/app.bin)拼接而成，需要将`bootloader.bin`填充至0x8000，`partition-table.bin`填充至0x10000，然后将三个文件直接合并。tools目录下提供了一个脚本以供使用，举例如下：
```
$./tools/esppad.sh bootloader.bin partition-table.bin app.bin flash_image.bin
```

## JTAG Debug
ESPLink 支持使用jtag调试ESP32-S3,具体使用说明如下：  
### Openocd 安装
```
$git clone https://github.com/espressif/openocd-esp32.git
$cd openocd-esp32
$./bootstrap
$./configure --enable-cmsis-dap
$make -j
$sudo make install
```

### 烧录efuse
efuse STRAP_JTAG_SEL bit需要烧录来启用jtag功能.
```
$espefuse.py -p /dev/ttyACM0 burn_efuse STRAP_JTAG_SEL
```

### Attach to ESP32-S3
将GPIO3 拉低到GND以启用GPIO的JTAG功能，上电开发板然后执行以下脚本，若一切正常，则可检测到ESP32-S3的idcode并attach
```
$cd openocd-esp32
$sudo ./src/openocd -s ${PWD}/tcl -f tcl/interface/cmsis-dap.cfg -f tcl/target/esp32s3.cfg
Open On-Chip Debugger  v0.10.0-esp32-20210902-1-gca6a6332-dirty (2021-10-07-14:45)
Licensed under GNU GPL v2
For bug reports, read
        http://openocd.org/doc/doxygen/bugs.html
adapter speed: 10000 kHz

WARNING: ESP flash support is disabled!
WARNING: ESP flash support is disabled!
force hard breakpoints
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
Info : CMSIS-DAP: SWD  Supported
Info : CMSIS-DAP: JTAG Supported
Info : CMSIS-DAP: FW Version = 0255
Info : CMSIS-DAP: Serial# = 0800000100440064430000014e504332a5a5a5a597969908
Info : CMSIS-DAP: Interface Initialised (JTAG)
Info : SWCLK/TCK = 1 SWDIO/TMS = 1 TDI = 1 TDO = 1 nTRST = 0 nRESET = 1
Info : CMSIS-DAP: Interface ready
Info : High speed (adapter_khz 10000) may be limited by adapter firmware.
Info : clock speed 10000 kHz
Info : cmsis-dap JTAG TLR_RESET
Info : cmsis-dap JTAG TLR_RESET
Info : JTAG tap: esp32s3.cpu0 tap/device found: 0x120034e5 (mfg: 0x272 (Tensilica), part: 0x2003, ver: 0x1)
Info : JTAG tap: esp32s3.cpu1 tap/device found: 0x120034e5 (mfg: 0x272 (Tensilica), part: 0x2003, ver: 0x1)
Info : esp32s3.cpu0: Debug controller was reset.
Info : esp32s3.cpu0: Core was reset.
Info : esp32s3.cpu1: Debug controller was reset.
Info : esp32s3.cpu1: Core was reset.
Info : Listening on port 3333 for gdb connections
```

### Debug
attach 成功之后，另外打开一个终端窗口，可以使用telnet或者gdb来进行调试

#### Debug with Gdb
```
$xtensa-esp32s3-elf-gdb -ex 'target remote 127.0.0.1:3333' ./build/blink.elf
(gdb) info reg
(gdb) s
(gdb) continue
```

#### Debug with telnet
```
$telnet localhost 4444
>reset
>halt
>reg
>step
>reg pc
>resume
```
```
# 产品链接
[nanoESP32-S3 Board]()

# 参考
### esp-idf
https://github.com/espressif/esp-idf
### esp32-s3 get-started
https://docs.espressif.com/projects/esp-idf/en/latest/esp32s3/get-started/
### esp32-s3
https://www.espressif.com/zh-hans/products/socs/esp32-s3
