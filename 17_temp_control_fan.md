# Arduino：温控风扇（PID 雏形）



## 说明

当温度超过设定阈值时自动开启风扇（用电机模拟），低于阈值时关闭。引入简易 PID 控制的雏形——用滞回（Hysteresis）控制来避免频繁启停。



## 硬件需求

- Arduino UNO ×1

- DHT11 ×1

- 小型直流电机 + L298N 驱动 ×1

- 5V/12V 外接电源



## 电路连接

| L298N | Arduino |

|-------|---------|

| IN1 | D5 |

| IN2 | D6 |

| ENA | D9（PWM 调速） |

| 12V | 外接电源 |

| GND | 共地 |

| OUT1/OUT2 | 电机两端 |



## 代码

```cpp

#include <DHT.h>



#define DHTPIN 2

#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);



const int IN1 = 5;

const int IN2 = 6;

const int ENA = 9;



const float TEMP_ON  = 28.0;  // 高于 28°C 开风扇

const float TEMP_OFF = 26.0;  // 低于 26°C 关风扇

// 2°C 滞回区间，避免频繁切换



bool fanRunning = false;



void setup() {

  pinMode(IN1, OUTPUT); pinMode(IN2, OUTPUT);

  pinMode(ENA, OUTPUT);

  // 停止

  digitalWrite(IN1, LOW);

  digitalWrite(IN2, LOW);

  analogWrite(ENA, 0);



  dht.begin();

  Serial.begin(9600);

}



void loop() {

  float temp = dht.readTemperature();

  if (isnan(temp)) { delay(1000); return; }



  Serial.print("温度: "); Serial.print(temp); Serial.println(" °C");



  if (temp > TEMP_ON && !fanRunning) {

    fanRunning = true;

    digitalWrite(IN1, HIGH);

    digitalWrite(IN2, LOW);

    // PWM 调速：温度越高越快

    int speed = map(temp, TEMP_ON, 40, 100, 255);

    speed = constrain(speed, 100, 255);

    analogWrite(ENA, speed);

    Serial.println(" → 风扇启动");

  }

  else if (temp < TEMP_OFF && fanRunning) {

    fanRunning = false;

    digitalWrite(IN1, LOW);

    digitalWrite(IN2, LOW);

    analogWrite(ENA, 0);

    Serial.println(" → 风扇停止");

  }



  delay(1000);

}

```



## 教学重点

- **滞回控制**：开和关有不同阈值，是工业 PID 的简化版

- `constrain(value, min, max)` 限幅函数

- L298N 电机驱动：IN1/IN2 控制方向，ENA 控制 PWM 调速

- 电机和 Arduino 必须共地



## 常见错误

- 只用单一阈值会导致风扇在临界点疯狂启停

- 忘记共地 → 电机不转或失控

- `analogWrite(ENA, 0)` 写错引脚 → 电机一直转

- 12V 供电时接线错误可能烧毁 Arduino

