# Arduino：多任务调度（非阻塞框架）



## 说明

不用 `delay()`，实现三个独立任务同时运行：LED 以不同频率闪烁、串口输出、传感器轮询。这是 Arduino 进阶的核心——从阻塞到非阻塞的思维转变。



## 硬件需求

- Arduino UNO ×1

- LED ×3（红/黄/绿），220Ω 电阻 ×3

- DHT11 ×1



## 代码

```cpp

#include <DHT.h>

#define DHTPIN 2

#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);



// 任务时间管理结构

struct Task {

  unsigned long interval;   // 执行间隔

  unsigned long lastRun;    // 上次运行时间

  void (*callback)();       // 任务函数指针

};



// 三个 LED 引脚

const int LEDS[] = {11, 10, 9};

int ledState[3] = {LOW, LOW, LOW};



// 任务定义

void taskLed1() {

  ledState[0] = !ledState[0];

  digitalWrite(LEDS[0], ledState[0]);

}



void taskLed2() {

  ledState[1] = !ledState[1];

  digitalWrite(LEDS[1], ledState[1]);

}



void taskLed3() {

  ledState[2] = !ledState[2];

  digitalWrite(LEDS[2], ledState[2]);

}



void taskReadSensor() {

  float temp = dht.readTemperature();

  if (!isnan(temp)) {

    Serial.print("温度: "); Serial.print(temp); Serial.println(" °C");

  }

}



void setup() {

  for (int i = 0; i < 3; i++) pinMode(LEDS[i], OUTPUT);

  dht.begin();

  Serial.begin(9600);

}



void loop() {

  static Task tasks[] = {

    { 200, 0, taskLed1 },       // LED1 每 200ms 闪烁

    { 500, 0, taskLed2 },       // LED2 每 500ms 闪烁

    { 1000,0, taskLed3 },       // LED3 每 1000ms 闪烁

    { 3000,0, taskReadSensor },  // 每 3 秒读传感器

  };



  unsigned long now = millis();



  for (int i = 0; i < 4; i++) {

    if (now - tasks[i].lastRun >= tasks[i].interval) {

      tasks[i].lastRun = now;

      tasks[i].callback();  // 执行任务

    }

  }

  // loop() 仍然快速返回，不阻塞

}

```



## 教学重点

- **核心思想转变**：不是"延时等待"，而是"检查时间到了没有"

- `struct Task` 封装任务参数，`函数指针` 实现回调

- `millis()` 约 50 天后溢出归零，长期运行需处理

- 这个框架是 RTOS（实时操作系统）的简化雏形



## 常见错误

- 任务执行时间 > 调度间隔 → 任务堆积

- `lastRun` 没更新 → 任务每次都执行

- 在回调里用 `delay()` → 退化为阻塞

- `static` 变量放 `loop()` 里只在第一次初始化

