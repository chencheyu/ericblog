---
date: 2024-10-25
publish: true
slug: b31f2d26-3c2e-4c6d-87d1-379ea36049b1
tags:
- CS/CAndCpp
title: Macro
uuid: b2c75dcd-685e-40cb-9868-600eb9f35add
---
本節在 MinGW32 GCC-6.3.0-1 環境下編譯

```c
#define Min(x, y) ((x) < (y) ? (x) : (y))
2 * Min(2, 3);          // 2 * ((2) < (3) ? (2) : (3));
Min(3, Min(4 ,5));      // ((3) < (((4) < (5) ? (4) : (5))) ? (3) : (((4) < (5) ? (4) : (5))));
Min(3, 4 < 5 ? 4 : 5);  // ((3) < (4 < 5 ? 4 : 5) ? (3) : (4 < 5 ? 4 : 5))
```

函数宏需要在參數跟代換區塊的最外圍加上小括號避免代換結果有非預期的運算子結合

```c
#define Min(x, y) ((x) < (y) ? (x) : (y))
int a = 1;
a = Min(++a, 2); // a = ((++a) < (2) ? (++a) : (2));
```

注意`++a`被執行兩次，目前沒有優雅地處理方法

```c
#define Min(x, y) {(x) < (y) ? (x) : (y);}
2 * Min(2, 3);     // compile error
int a = Min(2, 3); // compile error
```

其實大括號也可以區隔運算子，但不能 assign 與再跟其他運算子結合

```c
#define Subx(num) void Sub_##num(void) {printf("this is " #num "\n");}
Subx(0)   // void Sub_0(void) {printf("this is " "0" "\n");}
Subx(123) // void Sub_123(void) {printf("this is " "123" "\n");}
```

巨集利用字串化運算子及語彙基元帶入運算子(Token-pasting operator)，生成函數定義

```c
#define foo abc
#define gfoo(x) Foo_##x
#define gfooo(x) gfoo(x)
gfoo(foo)();  // Foo_foo();
gfooo(foo)(); // Foo_abc();
gfooo( foo)();         // Foo_abc();
gfooo(  foo       )(); // Foo_abc();
```

Token-pasting operator 會終止巨集替代
Token-pasting operator 會忽略前後空白

```c
#define print(s) printf(#s)
print(123\n);      // printf("123\n");
print(abc  \n);    // printf("abc \n");
print(abc *#@ \n); // printf("abc *#@ \n");
```

字串化運算子會字串化空白跟`#`，`&quot;'[]{}();` 不能正常字串化

```c
#define STRINGIZE(z) #z
#define ADD_SLASH_X(y) STRINGIZE(\x ## y)
#define SYMBOL_STR(x) ADD_SLASH_X(x)
printf(EXP_STR(35)); // "\x35" aka 5
printf(EXP_STR(61)); // "\x61" aka a
```

傳入 Ascii 的 16 進制值回傳該 Ascii 字串
[C macro : turn a number into a string](https://stackoverflow.com/questions/1595544/c-macro-turn-a-number-into-a-string)

```c
#if DLEVEL == 0
    #define STACK 0
#elif DLEVEL == 1
    #define STACK 100
#elif DLEVEL > 5
    display( debugptr );
#else
    #define STACK 200
#endif
```

Macro 條件判斷

```c
#define MASTER_BOARD
#if defined (MASTER_BOARD) // true
```

即便 define 沒有定義值，用 `defined (MASTER_BOARD)` 測試時仍然為真
