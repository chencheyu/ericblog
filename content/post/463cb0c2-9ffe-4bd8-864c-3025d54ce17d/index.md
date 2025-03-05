---
date: 2024-08-14
publish: true
slug: f1fee151-70b1-4cb1-9b2b-8a45493dec3f
tags:
- CS/Embedded
title: RaspberryPi
uuid: 3a58145c-1c44-4d02-b4f4-d6d5957ad1b2
---
- Pi3 的chip文件
  [BCM2837](../92ef7eed-5d57-4c6a-a429-e4f999cd02b3.pdf)

- Bare metal Raspberry Pi 3 tutorials
  [raspi3-tutorial](https://github.com/bztsrc/raspi3-tutorial)

- Pi4 的chip文件
  [Bcm2711](../9ebac9d8-97f3-40d8-b0ce-e5f75a958a38.pdf)

### Pin

- Raspberry Pi GPIO pin define
  [Raspberry Pi Pinout](https://pinout.xyz/)

- [pigg](https://github.com/andrewdavidmackenzie/pigg)
  GUI for View and Config RaspberryPi GPIO

GPIO 引腳可設定為高電平 3.3V 或低電平 0V，Pi 4 用電源供應器實測可以感受到 1 mA 3.3V，最低 1.2 還會判 Hight
![](../41b895ea-1347-488a-a295-35d250cd26f8.png)

### Raspberry Pi 1

#### Raspberry Pi I2C clock-stretching bug

[Raspberry Pi I2C clock-stretching bug](https://forums.raspberrypi.com/viewtopic.php?f=44&amp;t=13771&amp;p=405024)

- 時鐘拉伸僅在 I2C 讀取確認階段之後（讀取 ACK/NACK 後）直接起作用，並且僅當時鐘拉伸超過 0.5 個時脈週期時才起作用。
- 時鐘延長在 I2C 寫入確認階段開始時不起作用（即從機必須決定是否發送 ACK 或 NACK 的時刻）
- 除了從機可能會錯過太短的時脈的問題之外，當時脈拉長時， Raspberry Pi 還會過早讀取 SDA。因此，即使在 I2C 讀取確認階段將時鐘拉伸非常非常小，也會導致資料損壞。

### GPIO

#### GPIO sysfs

```sh
$ cd /sys/class/gpio
$ ls
export  gpiochip0  gpiochip504  unexport
$ echo 26 > export
$ ls
export  gpio26  gpiochip0  gpiochip504  unexport
$ ls gpio26
active_low  device  direction  edge  power  subsystem  uevent  value
$ cat gpio26/direction
in
$ echo out > gpio26/direction
$ cat gpio26/value
0
$ echo 1 > gpio26/value
```

古老的手段，準備棄用，非常慢
直接操作 /sys/class/gpio ，無法確定是否爭用，程式意外結束時不會被 OS 清理

#### Hardware Register

```c
// Open the master /dev/ memory control device
// Device strategy: December 2016:
//	Try /dev/mem. If that fails, then
//	try /dev/gpiomem. If that fails then game over.

  if ((fd = open ("/dev/mem", O_RDWR | O_SYNC | O_CLOEXEC)) < 0)
  {
    if ((fd = open ("/dev/gpiomem", O_RDWR | O_SYNC | O_CLOEXEC) ) >= 0)	// We're using gpiomem
    {
      piGpioBase   = 0 ;
      usingGpioMem = TRUE ;
    }
    else
      return wiringPiFailure (WPI_ALMOST, "wiringPiSetup: Unable to open /dev/mem or /dev/gpiomem: %s.\n"
	"  Aborting your program because if it can not access the GPIO\n"
	"  hardware then it most certianly won't work\n"
	"  Try running with sudo?\n", strerror (errno)) ;
  }
```

不能夠處裡 edge detection
wiringPi, pigpio, RPi.GPIO, and gpiozero’s “native” driver 使用此方式

> They’d “play nice” and use the sysfs interface for edge detection, but then stomp all over the hardware registers whenever they wanted to do anything else (reading the value of a GPIO, setting its direction, writing a value, etc.).

> #### gpiochip

> kernel 4.8 後支援
> 新增 /dev/gpiochip* 用 ioctl 在 user mode 控制 GPIO
> 會正確處裡所有權，在程式結束後刪除控制
> 用戶空間庫是 libgpiod
> `gpioinfo` 確定存在哪些 GPIO 以及它們當前是否正在使用任何事物



#### Lib

- [RPi.GPIO](https://pypi.org/project/RPi.GPIO)
  Pi 5 不可用

- [GPIOD](https://pypi.org/project/gpiod/)
  [libgpiod/libgpiod.git](https://git.kernel.org/pub/scm/libs/libgpiod/libgpiod.git/) 的 python 綁定

> server and client sides in the form of the gpio-manager daemon and the gpiocli command-line utility for talking to the manager
> 使用 /dev/gpiochip



- [gpiozero](https://gpiozero.readthedocs.io/en/latest/index.html)
  有設備抽象，Raspberry Pi OS 預設安裝
  純 python 實施，只會幫忙呼叫後端函式庫(預設RPi.GPIO)

- [pigpio library](https://abyz.me.uk/rpi/pigpio/python.html)
  [pigpio C](https://github.com/joan2937/pigpio) 的 python 綁定

- [WiringPi-Python](https://github.com/WiringPi/WiringPi-Python)
  [WiringPi](https://github.com/WiringPi/WiringPi/tree/master) 的 python 綁定
  WiringPi 於 2019 年 8 月被其作者棄用

### Raspberry Pi OS

Pi 3 裝 32 位作業系統速度較快，記憶體用量較小
Pi 4 裝 64 位作業系統速度較快 10% 以上
64Bit 會占用比 32 Bit 多的記憶體(指針及整數較長)

### Pi 4

在使用中發現即使 CPU 使用率小於 20% 但機器反應還是很慢，包括開 GUI 程式及移動滑鼠
- Raspberry Diagnostics
指令為 `agnostics`，需要 GUI
一個沒有官方文檔的程式檢查 SD 卡速度
Raspberry Diagnostics 要求 random read 1500 IOPS , random write 500 IOPS

- 修改滑鼠輪詢率
  在 /boot/firmware/cmdline.txt 末端加入選項 usbhid.mousepoll=8

- 0 表示按滑鼠要求

- 1 每秒 1000 次

- 2 每秒 500 次

- 確保電源供應器符合官方標準 (Raspberry Pi 4 1.2A 5V)

### [vcgencmd](https://elinux.org/RPI_vcgencmd_usage)

又是一個沒有官方文件的 tool，查詢及設定 Raspberry Pi 硬體

- vcgencmd measure_temp
  CPU 溫度，應小於 50 度 C

### Uart

> Raspberry Pi 上有兩種類型的 UART - PL011和 mini UART。 PL011 是一款功能強大、廣泛相容 16550 的 UART，而迷你 UART 的功能集則減少。



> Raspberry Pi 上的所有 UART 均為 3.3V - 如果將它們連接到 5V 系統，則會發生損壞。適配器可用於連接 5V 系統。或者，可以從各個第三方獲得低成本 USB 轉 3.3V 串行適配器



P3 只有 [UART - PL011](https://developer.arm.com/documentation/ddi0183/g/introduction/about-the-uart) 跟 mini UART 各一組，UART - PL011 被藍芽占用

P4 有 6 組 UART - PL011，0, 1 被藍芽占用

```
GPIO14 = TXD0 -> ttyAMA0
GPIO0  = TXD2 -> ttyAMA1
GPIO4  = TXD3 -> ttyAMA2
GPIO8  = TXD4 -> ttyAMA3
GPIO12 = TXD5 -> ttyAMA4
 
GPIO15 = RXD0 -> ttyAMA0
GPIO1  = RXD2 -> ttyAMA1
GPIO5  = RXD3 -> ttyAMA2
GPIO9  = RXD4 -> ttyAMA3
GPIO13 = RXD5 -> ttyAMA4
```

Raspberry Pi Pin pair with uart

### dtoverlay

device tree overlay
dtoverlay命令無法訪問啟動時加載的覆蓋；即該dtoverlay命令僅處理在運行時載入的覆蓋
an overlay or parameter applied by the firmware becomes "baked in" such that it won’t be listed by dtoverlay and can’t be removed.

- `dtoverlay -a` 列出所有可以 overlay 的 device
- `dtoverlay -h uart4` 列出參數
- `dtoverlay uart4` 載入
