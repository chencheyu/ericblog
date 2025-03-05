---
date: 2025-02-06
publish: true
slug: f0842eaa-d806-4806-b785-6d7975f8b087
tags:
- CS/ComputerSystemOrganization
title: SMBus
uuid: 2d59d400-459e-41c8-82a5-fb6000352c9b
---
[SMBus Specifications](https://smbus.org/specs/)
跟 [I2C](../f3268fa6-1407-4416-bd16-5e3c2e630c88.md) 很像的協議

## SMBus vs I2C

- I²C 匯流排可在任何時刻停滯（時鐘靜止），而不影響數據傳輸。主設備或從設備皆可將時鐘線保持在低電位，直到完成數據處理。
- SMBus 匯流排要求時鐘線不低於 10kHz，且不高於 100kHz，並設有 35ms 超時機制，若時鐘線維持低電位超過 35ms，則從設備將自動重置其介面。
- 由於 SMBus 要求時鐘不能靜止，因此 SMBus 需要一個最小時鐘速率，而 I²C 則沒有此限制
- 資料持留時間(Data Hold Time)的要求,SMBus 規定 SMBCLK 線路的準位下降後,SMBDAT 上的資料必須持續保留 300nS,但 I2C 卻沒有對此有相同的強制要求。
- I²C 允許從設備 (Slave) 在地址階段回應 NACK，當設備無法接收數據時 (例如正在執行即時任務)，可以拒絕通信。此時，主設備 (Master) 可以發送 STOP 信號 或 重啟 START 信號 以重新傳輸;SMBus 要求設備始終應答 (ACK) 自己的地址，以確保能夠偵測到 可移除設備 是否仍然連接至匯流排。

## Address

- 0000 1xxx = I2C HS-mode master code; SMBus reserved for future use.
- 0101 000x = SMBus reserved for ACCESS.bus host.
- 0110 111x = SMBus reserved for ACCESS.bus default address.
- 001 100x = SMBus Alert Response address.
- 1100 001x = SMBus Device Default address.
