# Arduino：读取模拟值（电位器）



## 说明

用电位器（可变电阻）读取模拟电压值，通过串口监视器查看读数，并用其控制 LED 亮度。学习 `analogRead()` ADC 转换和 `analogWrite()` PWM 输出。



## 硬件需求

- Arduino UNO ×1

- 10KΩ 电位器 ×1

- LED ×1，220Ω 电阻 ×1



## 电路连接

- 电位器中间脚接 A0，左右脚分别接 5V 和 GND

- LED 正极接 D9（PWM 引脚），负极通过 220Ω 接 GND



## 代码

```cpp

const int POT_PIN = A0;     // 电位器接模拟口 A0

const int LED_PIN = 9;      // LED 接 PWM 口 D9



void setup() {

  pinMode(LED_PIN, OUTPUT);

  Serial.begin(9600);       // 开启串口通信，波特率 9600

  Serial.println("电位器读数开始...");

}



void loop() {

  int rawValue = analogRead(POT_PIN);        // 读取 0~1023

  int brightness = map(rawValue, 0, 1023, 0, 255); // 映射到 PWM 范围



  analogWrite(LED_PIN, brightness);          // PWM 控制亮度



  Serial.print("原始值: ");

  Serial.print(rawValue);

  Serial.print("  ->  亮度: ");

  Serial.println(brightness);



  delay(100);  // 降低串口刷新速率

}

```



## 教学重点

- `analogRead()` 返回值范围是 0~1023（10 位 ADC）

- `analogWrite()` 范围是 0~255（8 位 PWM），仅限标有 `~` 的 PWM 引脚

- `map(value, fromLow, fromHigh, toLow, toHigh)` 进行数值范围映射

- 串口监视器（Ctrl+Shift+M）是调试 AVR 程序最重要的工具



## 常见错误

- PWM 引脚用错了（必须是 3, 5, 6, 9, 10, 11 中带 `~` 的）

- 电位器接错方向导致无法调节

- `map()` 函数参数顺序记错

