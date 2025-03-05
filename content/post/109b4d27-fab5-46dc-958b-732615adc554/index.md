---
date: 2024-08-02
publish: true
slug: 9bea2131-a6e8-4a63-8f12-2b41a49f32fd
tags:
- CS/CAndCpp
title: Mitigating Integer Overflow in C
uuid: 6322fb48-e2ed-45cd-98f5-3dd7678d966b
---
現代編譯器 Integer Overflow 行為調查
[Mitigating Integer Overflow in C](https://www.youtube.com/watch?v=PLcZkgHCk90) [Side](https://static.sched.com/hosted_files/lssna24/fb/Mitigating%20Integer%20Overflow%20in%20C.pdf)

#### Overflow

整數超過其所能表示的最大值
在 C 中，沒有任何語言層級的功能能處理

> Can’t calculate 3 * 128 (384) with 8 bit unsigned storage:
> 1 1000 0000 (9th bit has nowhere to go…) == 128
> 8 value bits (representation range of 0-255)



##### 現在機器中的處理方法

- None 由 CPU 決定
- Warp around to the end of range (回繞)
- Saturate 不超過最大值或最小值 ex: `refcount_t`
- Tarp 停止執行
- Undefined behavior

### C Standard For `unsigned int`

Warp around

### C Standard For `signed int`

Undefined behavior
這是統一 C 行為最難的地方
In practice this used to mean the “none” strategy, which for all sane
(2s-complement) systems would result in wrap-around.

```c
if (value + offset < value) {
	/* Overflow detected, reject bad offset. */ 3 
	return -EINVAL;
}
do_things_knowing_offset_is_safe(value + offset);
```

利用回繞做出的 Overflow 檢查
會被優化破壞掉

`-fno-strict-overflow` (-fwrapv) changes strategy for signed
integer overflow from undefined behavior to wrap-around

#### Sanitizer

Undefined Behavior Sanitizer’s arithmetic overflow checkers to the
rescue!
`-fsanitize=signed-integer-overflow`
`-fsanitize=unsigned-integer-overflow` (Clang only)
(also -fsanitize=pointer-overflow but not discussed today)
Two possible handlers
warn, but continue with calculation anyway (default)
trap, via `-fsanitize-trap=[SANITIZER|GROUP|all]`

對於 `-fsanitize=signed-integer-overflow` 時使用 `-fno-strict-overflow` 會確定使用 Warp around 而不是 Undefined Behavior
對於 `-fsanitize=unsigned-integer-overflow` 我們清楚的知道她會回繞，但這是 Undefined Behavior

```c
if (check_add_overflow(value, offset, &result)) {
 /* Overflow detected, reject bad offset. */
 return -EINVAL;
}
do_things_knowing_offset_is_safe(result);
```

強制檢查

```c
if (value + offset < value) { ... }
if (value + offset < 0) { ... }
```

Linux 還有利用 overflow 的行為
unsigned arithmetic has always been expected to wrap-around

```c
typedef unsigned int __attribute__((wraps)) u32_wrap;
```

```c
#define KVM_HVA_ERR_RO_BAD (-2UL) /* Hmm "ULONG_MAX - 1" is getting ugly */
```

As a short-hand for set all the bits or slightly less than max value
所以提供跳過檢查的語法

```c
void process_all(u32 num) { while (num--) process(num); }
```

Sanitizer 會報錯也會 overflow 但是是預期行為，Sanitizer 分析時要跳過

```c
u8 us
u32 ub = 255;
us = ub + 1;
```

超過賦值方向所能容忍的最大值
上方範例會通過 Sanitizer 檢查，下面追加兩支旗標偵測
`-fsanitize=implicit-signed-integer-truncation`
Has the widest coverage due to integer promotion, i.e. from signed
`-fsanitize=implicit-unsigned-integer-truncation`

### 修正狀態

- Linux v6.9
  Introduce CONFIG_UBSAN_SIGNED_WRAP for testing, which works
  with tip-of-tree Clang, but is still rough around the edges
- Linux v6.10 Aug 2024
  False positives found via syzkaller are addressed.
  CONFIG_UBSAN_SIGNED_WRAP ready for wider testing.
  Let Sanitizer work
- Clang 19.0 fix (signed, unsigned)
  integer overflow sanitizers work with -fno-strict-overflow
  wraps attribute available
- Gcc In 2024
  Need fix signed, unsigned integer overflow sanitizers

CPP 可以重載運算子在 overflow 時拋異常，以上問題在 C 才有
