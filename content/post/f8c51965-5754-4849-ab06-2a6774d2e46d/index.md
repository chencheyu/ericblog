---
date: 2025-02-28
publish: true
slug: 66d357e7-d624-49f3-a77a-9a82b3ca42f5
tags:
- CS/Backend
title: iSO 8601
uuid: ba736479-03e9-4af5-978d-e9b4ef06f136
---
標準日期字串
有兩套準 [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339)，iSO 8601
iSO 8601 一般比 RFC 3339 寬鬆

`2020-12-09T16:09:53+00:00` 是 RFC 3339，iSO 8601 都可以接受的字串

## ISO 8601

有 4 版目前最新是 2019 年版

- 時間單位的位數固定，不足時於左補0
- 日期依照年月日時分秒方式排序，如果需要降低精度要從最小的時間開始移除
- 格式分為只有數字的基礎格式；二為添加分隔符的擴展格式，從而更易讀。標準原文建議應避免在純文字中使用基礎格式。擴展格式使用連字元`-`來分隔日期，使用冒號`:`來分隔時間

ISO 8601:2004 規定了一個參考日期，即1875年5月20日但在 ISO 8601-1:2019刪除
對字元大小寫敏感

### Format

`%Y-%M-%DT%h:%m:%s`
`%Y-%M-%DT%h:%m:%s.%u`
`%Y%M%DT%h%m%s`
`%Y%M%DT%h%m%s.%u`

- `%Y` - Year, 4 digits, 0000 到 9999，但0000至1582的年份僅應在雙方有共識時用於通訊
- `%M` - Month, 2 digits, 01 到 12
- `%D` - Day, 2 digits, 01 到 31
- `%V` - Week Year, 4 digits
- `%W` - Week, 2 digits, 一年中的第幾周，週數在 01 到 53, 注意 XXXX 年 1 月 1 號不一定是 XXXX-W01
- `%w` - Week Day, 1 digits, 星期幾 1 到 7
- `%O` - Ordinal Day ,3 digits, 表示一年中的第幾天，001 到 366
- `%h` - Hour, 2 digits, (一天中的) 00 到 24，24小時制
- `%m` - Minute, 2 digits, 00 到 59
- `%s` - Second, 2 digits, 00 到 60，60 只表示潤秒
- `%u` - Second-Fraction, n digits, 拿來表達比秒還小的時間單位，位數由通訊雙方定義，表示 `%u *` $1 / 10^n$ 秒
- `%Z` - Zone Hour, 2 digits, including `+/-`
- `%z` - Zone Minute, 2 digits

T00:00:00 指涉日曆日開始時刻
T24:00:00 指涉日曆日結束時刻，ISO 8601-1:2019 刪除儘管在早期版本的標準中已經允許這樣做

### Sample

20250203T163855+0800
2025-02-03T16:38:55+0800

20040503T173008+08
2004-05-03T17:30:08+08:00

### Time Zone

`%Z:%z`
`%Z%z`
`%Z`
`Z`

時區，可選，接在時間後面，如果時區沒有標示表示本地時間，Z 表示 UTC + 00，`-00` 不被允許

### Ordinal Date

`%Y-%O`
`%Y%O`

可以將一年內的天的序數用3位數字表示

如 2004 年 5 月 3 日可以表示為 2004-124 或 2004124

### Week Date

`%V-W%W-%w`
`%VW%W%w`
很麻煩，別用

2007-W03  2007 年的第 3 周
2007-W03-1 2007 年的第 3 周的星期一

## [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339)

只接受 `%Y-%M-%DT%h:%m:%s%Z:%z`, `%Y-%M-%D%Z:%z`, `%h:%m:%s%Z:%z`
不接受由小時間開始省略的規則
不接受 Ordinal Date，Week Date
對字元大小寫不敏感
一定要寫時區，只接受 `%Z:%z` 或 Z 作為時區格式，`-00` 是允許的

> 4.3 Unknown Local Offset Convention
> If the time in UTC is known, but the offset to local time is unknown, this can be represented with an offset of "-00:00"



2024 年 4 月，RFC 3339 由 RFC 9557 更新

## Reference

- [IJMacD/rfc3339-iso8601](https://github.com/IJMacD/rfc3339-iso8601)
  列出 iSO 8601 與 RFC 3339 許可的時間格式
