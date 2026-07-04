# Arduino：红外遥控解码



## 说明

用红外接收头解码家用遥控器的按键码，根据按键控制 LED 和蜂鸣器。学习红外通信协议、IRremote 库的使用。



## 硬件需求

- Arduino UNO ×1

- VS1838B 红外接收头 ×1

- 红外遥控器 ×1

- LED ×1，蜂鸣器 ×1



## 电路连接

| VS1838B | Arduino |

|---------|---------|

| OUT | D7 |

| VCC | 5V |

| GND | GND |



## 代码

```cpp

#include <IRremote.h>



const int IR_PIN = 7;

const int LED_PIN = 13;

const int BUZZER_PIN = 8;



IRrecv irrecv(IR_PIN);

decode_results results;



void setup() {

  Serial.begin(9600);

  irrecv.enableIRIn();      // 启动红外接收

  pinMode(LED_PIN, OUTPUT);

  pinMode(BUZZER_PIN, OUTPUT);

  Serial.println("红外接收就绪，请按遥控器...");

}



void loop() {

  if (irrecv.decode(&results)) {

    unsigned long code = results.value;

    Serial.print("收到码: 0x");

    Serial.println(code, HEX);



    // 根据键码执行操作

    switch (code) {

      case 0xFFA25D:  // 电源键

        digitalWrite(LED_PIN, !digitalRead(LED_PIN));

        Serial.println(" → LED 切换");

        break;

      case 0xFF629D:  // 音量+ 键

        tone(BUZZER_PIN, 1000, 200);

        Serial.println(" → 蜂鸣器响");

        break;

      case 0xFFE21D:  // 功能键

        Serial.println(" → 自定义功能");

        break;

      default:

        Serial.println(" → 未定义操作");

    }



    irrecv.resume();  // 接收下一个

  }

}

```



## 教学重点

- 红外协议：38KHz 载波 + NEC 编码（地址码+数据码+反码）

- `irrecv.decode()` 阻塞等待红外信号

- `irrecv.resume()` 必须调用，否则不能接收下一条

- 每个遥控器键码不同，需先解码确认再用



## 常见错误

- 接收头接反（中间是 GND，两边是 VCC/OUT）

- 忘记 `resume()` → 只能收到第一帧

- 强光干扰（阳光/日光灯会影响红外接收）

- 遥控器太远（有效距离约 5~8 米）

