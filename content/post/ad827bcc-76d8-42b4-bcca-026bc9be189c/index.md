---
date: 2025-02-20
publish: true
slug: 165d5732-73d6-4e7e-a891-dd95333d593a
tags:
- CS/Embedded/Arterytek
title: Arterytek
uuid: ec59d0a7-4013-4b2e-86dd-1a7e5abfdd31
---
## 函数加载到 Flash 或 SRAM 指定地址

将函数加载到 Flash 指定地址：

```c
void Test(void) __attribute__((section(".ARM.__at_0x08001000")));
void Test(void)
{}
```

将函数加载到 SRAM 指定地址

```c
void Test(void) __attribute__((section(".ARM.__at_0x20001000")));
void Test(void)
{}
```

只在 Keil 上有效

> - [arterychip FAQ0050](https://www.arterychip.com/download/FAQ/FAQ0050_%E5%B0%86%E5%87%BD%E6%95%B0%E5%8A%A0%E8%BD%BD%E5%88%B0Flash%E6%88%96SRAM%E6%8C%87%E5%AE%9A%E5%9C%B0%E5%9D%80%E7%9A%84%E6%96%B9%E6%B3%95_V2.0.4_CH.pdf)
