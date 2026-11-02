# 电控组第二次作业
-**李豪博**
-***20263001726***
## 流水灯实现思路：
在CubeMX中将A0命名为LED0，将A3命名为LED1，将A7命名为LED2，并配置为`GPIO_Output`
 生成了宏定义：
 ```c
 #define LED0_Pin GPIO_PIN_0
 #define LED0_GPIO_Port GPIOA
 #define LED1_Pin GPIO_PIN_3
 #define LED1_GPIO_Port GPIOA
 #define LED2_Pin GPIO_PIN_7
 #define LED2_GPIO_Port GPIOA
```
## 非阻塞延时原理说明

基于 `HAL_GetTick()` 函数实现了时间戳差值法

首先定义
```c
#define LED_0 0
#define LED_1 1
#define LED_2 2
定义 `lastTick` 记录上一次切换LED的时间，定义 `step` 记录当前的状态

初始：此时时间为0，LED0亮，`step==LED_0`
```c
uint32_t lastTick=0;
uint8_t step = LED_0;
HAL_GPIO_WritePin(LED0_GPIO_Port, LED0_Pin, GPIO_PIN_SET);
HAL_GPIO_WritePin(LED1_GPIO_Port, LED1_Pin, GPIO_PIN_RESET);
HAL_GPIO_WritePin(LED2_GPIO_Port, LED2_Pin, GPIO_PIN_RESET);
在主循环里，通过 `HAL_GetTick()` 获取现在的时间，判断当前时间与 `lastTick` 的差值是否达到设定延时500ms。
 当时间差满足条件时，更新 `lastTick` 为当前时间，熄灭所有LED，`step`自增循环，再点亮对应LED；
 若未达到时间间隔，不执行灯光切换，直接进入下一轮循环，不会阻塞程序运行。
 ```c
 while (1)
 {
   uint32_t nowTick = HAL_GetTick();
   if(nowTick - lastTick >= 500)
   {
     lastTick = nowTick;
     // 熄灭所有灯
     HAL_GPIO_WritePin(LED0_GPIO_Port, LED0_Pin, GPIO_PIN_RESET);
     HAL_GPIO_WritePin(LED1_GPIO_Port, LED1_Pin, GPIO_PIN_RESET);
     HAL_GPIO_WritePin(LED2_GPIO_Port, LED2_Pin, GPIO_PIN_RESET);
     step = step + 1;
     if(step > LED_2)
     {
       step = LED_0;
     }
     if(step == LED_0)
       HAL_GPIO_WritePin(LED0_GPIO_Port, LED0_Pin, GPIO_PIN_SET);
     else if(step == LED_1)
       HAL_GPIO_WritePin(LED1_GPIO_Port, LED1_Pin, GPIO_PIN_SET);
     else if(step == LED_2)
       HAL_GPIO_WritePin(LED2_GPIO_Port, LED2_Pin, GPIO_PIN_SET);
   }
 }
