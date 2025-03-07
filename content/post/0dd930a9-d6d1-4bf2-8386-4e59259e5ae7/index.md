---
date: 2024-12-02
publish: true
slug: 6a87e9b5-a9ff-4ccd-bcdd-055fc6c0a4f8
tags:
- CS/ComputerSystemOrganization
title: I2C.md
uuid: 442c2e9b-aba8-484c-98db-45105492e1d8
---
### Frequencies

- Standard mode: 100 kilobits per second.
- Fast mode: 400 kilobits per second.

## 時序

在 UM10204 6 Electrical specifications and timing for I/O stages and bus lines，需要時請直接查閱
![](../daaade24-5474-4e23-8fb7-3f62460532a2.png)

- t<sup>r</sup>
  SCL, SDA 上升或下降從 70% 到 30% 的時間
  Max Standard 300 ns, Fast 300 ns
- t<sup>LOW</sup>
  SCL 維持低電平
  Min Standard 4.7 μs, Fast1.2 μs
- t<sup>HIGH</sup>
  SCL 維持高電平
  Min Standard 4.0 μs, Fast0.6 μs
- t<sup>HD;DAT</sup>
  SCL 下降到SDA 狀態改變完成的時間
  沒有最小時間規範
- t<sup>SU;DAT</sup>
  SDA 狀態改變完成到 SCL 重新拉高的時間
  Min Standard 250 ns, Fast 100 ns

## Data Transfer

![](../b96e52ca-da04-47b8-b1e5-1bdf00117dd0.png)
所有位元組均以 MSB 位元先移位的方式傳送
除了啟動和停止訊號外，SDA 線僅在時脈為低電平時變化

### 0. 啟動

主設備發起的每個 I2C 指令都以START 條件開始，以STOP 條件結束。對於這兩種情況，SCL 必須維持高。 SDA 從高到低的轉換被視為START，從低到高的轉換被視為STOP

### 1. ADDRESS

在啟動條件之後，主設備可以產生重複的啟動。這相當於正常的啟動，並且通常後面跟著從 I2C 位址
每一次啟動後 Master 通常會向 bus 寫 slave 的地址(7 bit or 10 bit)加上 1 bit 的讀寫指示(1 = Read
, 0 = Write)，Slave 須向 master 寫 ACK

### 2. DATA

需要一些時間來處理接收到的位元組或尚未準備好發送下一個位元組的從設備可以將時鐘拉低以向主設備發出信號以指示其應該等待。一旦時鐘被釋放，主設備就可以繼續處理下一個位元組

### 3. 停止

SCL 必須維持高，SDA 從低到高的轉換被視為STOP

### repeated START

![](../d8b6f8b6-57d5-4a3e-9733-f03d6c314dc9.png)
如果控制器仍然希望在总线上通信，它可以生成一个重复的 START 条件 (Sr) 并寻址另一个目标，而无需首先生成 STOP 条件
如果產生重複的 START (Sr) 而不是 STOP 條件，則匯流排保持忙碌。在這方面，START (S) 和重複的 START (Sr) 條件在功能上是相同的
如果送一个重复的 START 条件，可以在重复的 START 条件之前发送一个非确认，如附圖

## Write

![](../e322ece4-d1a8-42b9-9d3b-930c1b76bb1b.png)
![](../c9cf651c-a0bc-49bd-b8c5-2b8917e0a187.png)
Master 對 Slave (Address 0x50) 寫入 0x1133
![](../9d29d204-6eed-462d-8112-64e13b987844.png)
Master 對 Slave (Address 0x0B) 寫入 0x442100
CLK 不需要是穩定一致的，Slave 可以拉住 CLK

## Read

![](../96bf2bb8-ed1d-4d91-83f4-9be6917f4c25.png)
![](../6234b91b-277e-4a63-af60-58937d94c5e0.png)
灰色部份表示 Master to Slave，白色部分為 Slave to Master
Master 對 Slave (Address 0x50) 讀到 0x3300
在 read 資料的最後一個 byte 必須要是 NACK

## Write Then Read

![](../9b3e0376-4ca4-4d24-9820-7bab8953444c.png)
Master 對 Slave (Address 0x5B) 寫入 0xAA 後直接讀到 0x00

## Reset Bus

- 對於將 SCL/SDA 腳位與 GPIO 多工使用的主裝置，最簡單的方法是將這些腳位配置為 GPIO 模式，並通過切換 SCL 來操作，直到從裝置釋放 SDA。此時，您應該能夠恢復正常操作

## Reserved addresses

- 0000 0000 general call address
- 0000 0001 START byte
- 0000 001X CBUS address
- 0000 010X reserved for different bus format
- 0000 011X reserved for future purposes
- 0000 1XXX Hs-mode controller code
- 1111 1XX1 device ID
- 1111 0XXX 10-bit target addressing
  此外，如果確定某個保留地址 (Reserved Address) 不會用於其原始用途，則該保留地址也可以被用作目標設備的地址。但這種做法需要謹慎考量，以避免與標準 I²C 協議的潛在衝突

## I2C Ack

![](../683ec36b-e8ea-4bbf-a8a8-1d2b03ee7162.png)
Ack 時，如果是 master 要把 SDA 的控制權轉給 slave 寫 Ack bit 時，可能會發生 SDA 突然短暫被拉高的現象，這是正常的

## Chip

- [TCA9548A](https://www.ti.com/lit/ds/symlink/tca9548a.pdf)
  Low-Voltage 8-Channel I2C Switch with Reset

## 常見錯誤

### 仲裁丢失（ARLOST）

当设备控制 SDA 线输出高电平，但是总线上实际输出低电平时，发生仲裁丢失事件。
― 主机发送数据：仲裁可以发生在地址传输、数据传输阶段；
― 主机接收数据：仲裁可以发生在地址传输、响应ACK阶段；
― 从机发送数据：仲裁可以发生在数据传输阶段；
― 从机接收数据：仲裁可以发生在响应ACK阶段。
当在发生仲裁丢失后，硬件自动将 I2C_STS 的 ARLOST 置 1，无论是主机还是从机，都将会立即释放
SCL、SDA 总线，并自动回到从机状态。

### 总线错误(BUSERR)

在数据传输阶段，在 SCL 高周期区间 SDA 线必须保持稳定，当 SCL 信号为低时，SDA 才能改变，否则将会出现总线错误

### Reference

- [UM10204 I2C-bus specification and user manual](https://www.nxp.com/docs/en/user-guide/UM10204.pdf)
