---
date: 2024-02-22
publish: true
slug: 2e29d3fa-9701-4855-98f4-2e0e4bf6d359
tags:
- CS/NetWork
title: MQTT.md
---
### 簡介

用於 IOT 裝置的消息傳送協議，可在不穩定的網路下運作

### Publish Subscribe

Publisher 向一個 topic 發布消息， Subscriber 從訂閱的 topic 中接收消息，在同一個 topic 中 Publisher 和 Subscriber 都可以有多個客戶端同時使用

### Topic

- UTF-8 string
- 如`Germany/Bavaria/car/2382340923453/latitude` 用 `/` 表示層級
- 訂閱時可以使用通配符，`+` 選取同一層級，`#` 選取同一層級及往下所有層級，如`#` 表示機器上所有的 topic, Topic 區分大小寫
- 客戶端在發布或訂閱主題之前不需要建立所需的主題。代理接受每個有效主題，無需任何事先初始化。
- 前綴 `$SYS/` 表示 broker 內部狀態，不會被 `#` 選取
- 雖然可以，但不建議斜線開頭

### QOS

- 0 只傳送一次，射後不理
- 1 自少傳送一次，訊息可能會重複
- 2 一定只傳送一次
  QOS 會引響 broker 與客戶端的封包發送量
  QOS,封包數
  0,1
  1,2
  2,4
  Publisher 和 Subscriber 可以使用不同的 QOS

### 持久化

- broker 會記住 Subscriber 的訂閱，當同一個 ClientId 重新連線時
- 當 QOS 為 1 和 2 時維持尚未確認消息
- ClientId 應特定於每個客戶和經紀商。如果代理不需要維護任何狀態，則 MQTT 3.1.1 允許使用空 ClientId。但是，此連線必須將 clean_session 標誌設為 true，否則代理將拒絕該連線。
- clean_session 為 True 時表示不需要持久化

### Retain

原則上，broker 不會保留 Publish 的訊息，訊息要不被傳送給 Subscriber 要不被拋棄，Publish 的 Retain 選項會讓 broker 保留最後一則訊息

### [Paho Mqtt](https://eclipse.dev/paho/files/paho.mqtt.python/html/index.html)

eclipse 寫的純 python 庫
`client = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2, clean_session=True)`clean_session 必須要是 True 否則行為很怪

### Last Will and Testament

連線時設定，在客戶端未使用 DISCONNECT 斷開連線時由 broker 發送，跟一般的消息結構一致

### Tool

![MqttTool](../abd3de75-6e22-4fec-a859-031d0dc09f57.md)

### Public MQTT Broker

- [EMQX Public MQTT Broker](https://www.emqx.com/en/mqtt/public-mqtt5-broker)
- [Mosquitto](test.mosquitto.org)

### Ref

[MQTT Essentials Guide and eBook](https://www.hivemq.com/resources/content/download-mqtt-ebook/)
