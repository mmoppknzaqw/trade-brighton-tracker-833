# Arduino：EEPROM 断电记忆计数器



## 说明

制作一个按钮计数器，每次按下数字加 1，断电重启后数值不丢失。学习 Arduino 内置 EEPROM 的读写操作。



## 硬件需求

- Arduino UNO ×1

- 按键 ×2（计数 + 清零）

- （可选）LCD1602 显示屏



## 代码

```cpp

#include <EEPROM.h>



const int BTN_COUNT = 2;

const int BTN_RESET = 3;

const int EEPROM_ADDR = 0;  // 存储地址



unsigned int counter = 0;



void setup() {

  Serial.begin(9600);

  pinMode(BTN_COUNT, INPUT_PULLUP);

  pinMode(BTN_RESET, INPUT_PULLUP);



  // 从 EEPROM 读取上次保存的值

  EEPROM.get(EEPROM_ADDR, counter);

  Serial.print("启动！当前计数: ");

  Serial.println(counter);

}



void loop() {

  if (!digitalRead(BTN_COUNT)) {

    counter++;

    EEPROM.put(EEPROM_ADDR, counter);  // 立即保存

    Serial.print("计数: ");

    Serial.println(counter);

    delay(300);  // 消抖

  }



  if (!digitalRead(BTN_RESET)) {

    counter = 0;

    EEPROM.put(EEPROM_ADDR, counter);

    Serial.println("计数已清零！");

    delay(300);

  }

}

```



## 教学重点

- `EEPROM.get(address, variable)` 从 EEPROM 读取任意类型数据

- `EEPROM.put(address, variable)` 写入任意类型数据

- EEPROM 有擦写寿命（约 10 万次），频繁写入需注意磨损平衡

- UNO 有 1024 字节 EEPROM，够存大量数据



## 常见错误

- 地址越界（超过 1024）→ 数据可能写到错误位置

- 没调用 `EEPROM.put()` → 断电数据丢失

- 使用 `EEPROM.write()` 逐字节写入比 `put()` 麻烦且容易错

- 上电时没读取就显示 0

