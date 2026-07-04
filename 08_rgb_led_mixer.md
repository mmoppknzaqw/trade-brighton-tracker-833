# Arduino：RGB LED 色彩混合器



## 说明

用三个电位器分别控制 RGB LED 的红、绿、蓝通道，实现任意颜色混合。学习 PWM 调光原理、三原色加法混色、三个模拟口的并行读取。



## 硬件需求

- Arduino UNO ×1

- RGB LED（共阴或共阳）×1

- 10KΩ 电位器 ×3

- 220Ω 电阻 ×3



## 电路连接

RGB LED 共阴型：最长脚接 GND，红/绿/蓝短脚各通过 220Ω 电阻接 D9/D10/D11。

电位器：中间脚分别接 A0/A1/A2，左右脚接 5V/GND。



## 代码

```cpp

const int RED_PIN   = 9;   // 红色 PWM

const int GREEN_PIN = 10;  // 绿色 PWM

const int BLUE_PIN  = 11;  // 蓝色 PWM



const int POT_RED   = A0;  // 红通道电位器

const int POT_GREEN = A1;  // 绿通道电位器

const int POT_BLUE  = A2;  // 蓝通道电位器



void setup() {

  pinMode(RED_PIN, OUTPUT);

  pinMode(GREEN_PIN, OUTPUT);

  pinMode(BLUE_PIN, OUTPUT);

  Serial.begin(9600);

}



void loop() {

  // 读取三个电位器并映射到 PWM 范围

  int r = map(analogRead(POT_RED),   0, 1023, 0, 255);

  int g = map(analogRead(POT_GREEN), 0, 1023, 0, 255);

  int b = map(analogRead(POT_BLUE),  0, 1023, 0, 255);



  // 输出色彩

  analogWrite(RED_PIN, r);

  analogWrite(GREEN_PIN, g);

  analogWrite(BLUE_PIN, b);



  Serial.print("RGB: (");

  Serial.print(r); Serial.print(", ");

  Serial.print(g); Serial.print(", ");

  Serial.print(b); Serial.println(")");



  delay(50);

}

```



## 教学重点

- RGB 加法混色：R+G+B = 白色，R+G = 黄色，G+B = 青色，R+B = 品红

- 三个 `map()` 函数将 ADC 读数统一映射到 PWM 输出范围

- 共阴 vs 共阳：共阳 RGB 需改为 `analogWrite(pin, 255 - value)` 取其补色



## 常见错误

- RGB LED 引脚混淆：红/绿/蓝脚要对到正确 PWM 口

- 忘记限流电阻：LED 直接接 IO 口会烧毁

- 共阴/共阳搞错导致颜色反转

