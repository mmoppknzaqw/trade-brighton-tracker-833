# Arduino：LCD1602 液晶屏显示



## 说明

用 I2C 接口的 LCD1602 显示屏显示文字和传感器数据。学习 I2C 通信协议、LiquidCrystal_I2C 库的高级用法、字符串格式化输出。



## 硬件需求

- Arduino UNO ×1

- LCD1602 液晶屏（I2C 接口）×1

- DHT11 温湿度传感器 ×1



## 电路连接

| I2C LCD | Arduino |

|---------|---------|

| VCC | 5V |

| GND | GND |

| SDA | A4 |

| SCL | A5 |



## 代码

```cpp

#include <Wire.h>                  // I2C 通信库（内置）

#include <LiquidCrystal_I2C.h>     // LCD I2C 库（需安装）

#include <DHT.h>



// LCD 地址 0x27，16列2行

LiquidCrystal_I2C lcd(0x27, 16, 2);



#define DHTPIN 2

#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);



void setup() {

  lcd.init();            // 初始化 LCD

  lcd.backlight();       // 打开背光

  lcd.setCursor(0, 0);

  lcd.print("Hello Arduino!");



  dht.begin();

  delay(2000);

  lcd.clear();           // 清屏

}



void loop() {

  float temp = dht.readTemperature();

  float hum = dht.readHumidity();



  if (isnan(temp) || isnan(hum)) {

    lcd.setCursor(0, 0);

    lcd.print("Sensor Error!  ");

    return;

  }



  // 第一行：温度

  lcd.setCursor(0, 0);

  lcd.print("Temp: ");

  lcd.print(temp, 1);   // 保留 1 位小数

  lcd.print((char)223); // 显示 ° 符号

  lcd.print("C   ");



  // 第二行：湿度

  lcd.setCursor(0, 1);

  lcd.print("Humi: ");

  lcd.print(hum, 1);

  lcd.print("%   ");



  delay(1000);

}

```



## 教学重点

- I2C 只需 2 根线（SDA 数据 + SCL 时钟）即可通信

- `lcd.setCursor(col, row)` 设置光标位置

- `lcd.print(var, decimal)` 控制浮点数小数位数

- 末尾加空格 `"   "` 覆盖旧字符，防止残留



## 常见错误

- 屏幕不显示：I2C 地址不是 0x27（扫描工具确认）

- 屏幕只有背光无字符：调整背面电位器对比度

- 字符闪烁：`delay()` 太短，数据还没稳定就刷新了

- `(char)223` 在不同编码下可能乱码

