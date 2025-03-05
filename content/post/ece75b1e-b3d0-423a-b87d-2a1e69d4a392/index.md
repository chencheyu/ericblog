---
date: 2024-10-09
publish: true
slug: bf86cc87-0634-4352-865b-8ce49f9be36f
tags:
- CS/Embedded/Arterytek
title: AT32F425 pwc_voltage_regulate_set
uuid: 16e5de0b-ec57-4d93-a7bd-18fa049c7502
---
在將 AT32F425 BSP 從 V2.0.0 更新到 V2.1.2 發現 pwc_voltage_regulate_set() 行為有差異

### AT32F425 BSP 应用指南

查閱 `AT32F425固件库BSP&amp;Pack应用指南` 5.14.10 函数 pwc_voltage_regulate_set

> 下表描述了函数 pwc_voltage_regulate_set
> 表 365. 函数 pwc_voltage_regulate_set
> 项目
> 描述
> 函数名
> pwc_voltage_regulate_set
> 函数原型
> void pwc_voltage_regulate_set(pwc_regulator_type pwc_regulator)
> 功能描述
> 深度睡眠模式下电压调节器状态选择
> 输入参数
> pwc_regulator：电压调节器状态选择
> 该参数可以选取 pwc_regulator_type 内的任意一个枚举值.
> pwc_regulator
> pwc_regulator 用于选择电压调节器的状态，其可选参数罗列如下
> PWC_REGULATOR_ON：
> 深度睡眠模式下电压调节器正常开启
> PWC_REGULATOR_LOW_POWER：
> 深度睡眠模式下电压调节器处于低功耗模式
> PWC_REGULATOR_EXTRA_LOW_POWER：
> 深度睡眠模式下电压调节器处于额外低功耗模式
> 示例

> ```c
> /* congfig the voltage regulator mode */
> pwc_voltage_regulate_set(PWC_REGULATOR_LOW_POWER);
> ```



確定 pwc_regulator 有三個選項

### Implementation

#### V2.0.0

```c
typedef enum{
  PWC_REGULATOR_ON                       = 0x00, /*!< voltage regulator state on when deepsleep mode */
  PWC_REGULATOR_LOW_POWER                = 0x01,  /*!< voltage regulator state low power when deepsleep mode */
  PWC_REGULATOR_EXTRA_LOW_POWER          = 0x02  /*!< voltage regulator state extra low power when deepsleep mode */
} pwc_regulator_type;
void pwc_voltage_regulate_set(pwc_regulator_type pwc_regulator){
  switch(pwc_regulator){
    case 0:
      PWC->ctrl2_bit.vrexlpen = 0;
      PWC->ctrl_bit.vrsel = 0;
      break;
    case 1:
      PWC->ctrl2_bit.vrexlpen = 0;
      PWC->ctrl_bit.vrsel = 1;
      break;
    case 2:
      PWC->ctrl2_bit.vrexlpen = 1;
      PWC->ctrl_bit.vrsel = 1;
      break;
    default: 
      break;
  }
}
```

#### V2.1.2

```c
typedef enum{
  PWC_REGULATOR_ON                       = 0x00, /*!< voltage regulator state on when deepsleep mode */
  PWC_REGULATOR_LOW_POWER                = 0x01  /*!< voltage regulator state low power when deepsleep mode */
} pwc_regulator_type;

void pwc_voltage_regulate_set(pwc_regulator_type pwc_regulator){
  switch(pwc_regulator){
    case PWC_REGULATOR_ON:
      PWC->ctrl_bit.vrsel = FALSE;
      break;
    case PWC_REGULATOR_LOW_POWER:
      PWC->ctrl_bit.vrsel = TRUE;
      break;
    default:
      break;
  }
}
```

顯然 V2.1.2 更改的了 pwc_voltage_regulate_set 實作方法跟選項但文件並未更改

### Solve

廠商回英文郵件已更改

原因應為 ES0004_AT32F425_Errata_Sheet_V2.0.7_CH.pdf

> 1.4.4 DEEPSLEEP 内部电压调节器额外低功耗模式无法使用 问题描述： DEEPSLEEP模式下，操作内部电压调节器额外低功耗模式使能位（VREXLPEN），可能会误修 改到内部电压调节器默认值，从而导致芯片运行异常。 解决方法： 无，不操作位于 PWC_CTRL2 寄存器的内部电压调节器额外低功耗模式使能位（VREXLPEN）
