---
date: 2018-08-02
publish: true
slug: 32af59e2-0a8d-4fbc-aff6-ac3182abac8a
tags:
- CS/CAndCpp
title: "c\u8A9E\u8A00\u6559\u5B78.md"
---
### 型別

就是我們對一串資料的態度
先看一串資料
請幻想有一堆格子，每隔多只有0跟1兩種可能，存取方式不限

## 整數

```c
unsigned int i;
```

連續16 or 32 or 64 bit
0x0000 -> 0
0x0001 -> 1
0xffff -> 65535

```c
int i;
```

我想用最左的bit當正負記號1為負
然後就出現兩個0了0x8000 0x0000
所以把0xffff 當-1
0xfffe -> -2
正負轉換為 (~i)+1

## 字元

```c
char c;
```

其實就是一個8bit整數
一般是ascii

## Array

```c
int arr[10];
```

用 `[]` 表示一堆連續的資料，如上述範例表示10個連續的整數

## C String

```c
char s[64];
```

用零結尾的連續字元，如果零在第3個位子，那字串就只有3個字元
