---
date: 2024-06-21
publish: true
slug: 446c60e9-03ca-4944-b3ab-5c73e1ed00ee
tags:
- CS/CAndCpp
title: "\u53EF\u8B8A\u53C3\u6578\u7684 C \u51FD\u6578"
---
### VariadicFunction

```c
#include <stdarg.h>  
void error(const char *fmt, ...)  
{  
va_list argp;  
va_start(argp, fmt);  
vfprintf(stderr, fmt, argp);  
va_end(argp);  
}  
```

用 `...`表達接受可變數量的參數，`va_list argp` 是一個指向第一個可變參數的指針，無法知道長度及型態，[沒有可移植](<%5Bhttps://c-faq.com/varargs/invvarargs.19920714.html%5D(https://c-faq.com/varargs/invvarargs.19920714.html>)) 的方法把可變數量的參數傳給下一個可變數量的參數的函式，有一些相依CPU的[方法](https://c-faq.com/varargs/invvarargs.19890604.html)，有部分可變數量參數的函式也提供接收 `va_list ` 的版本如 `vprintf, vfprintf, vsprintf`

```cpp
#define my_printf(format, ...) \  
do { \  
fprintf(stderr, "Calling printf with fmt %s\n", format); \  
some_other_variadac_function(format, ##__VA_ARGS__); \  
} while(0)  
```

### Variadic Macros

```c
#define ProxDebugPrintf(...) printf(__VA_ARGS__)
```

[Variadic Macros](https://gcc.gnu.org/onlinedocs/cpp/Variadic-Macros.html) 可以傳遞可變數量的參數
