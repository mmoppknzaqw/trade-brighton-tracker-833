# Arduino：矩阵键盘密码锁



## 说明

用 4×4 矩阵键盘输入密码，正确则亮绿灯开锁，错误亮红灯报警。学习矩阵键盘扫描原理、Keypad 库、密码校验逻辑。



## 硬件需求

- Arduino UNO ×1

- 4×4 矩阵键盘 ×1

- 红色 LED ×1，绿色 LED ×1

- 蜂鸣器 ×1



## 电路连接

键盘行引脚接 D5~D8，列引脚接 D9~D12。LED 接 D3(红)/D4(绿)。



## 代码

```cpp

#include <Keypad.h>



const byte ROWS = 4; // 四行

const byte COLS = 4; // 四列



char keys[ROWS][COLS] = {

  {'1','2','3','A'},

  {'4','5','6','B'},

  {'7','8','9','C'},

  {'*','0','#','D'}

};



byte rowPins[ROWS] = {5, 6, 7, 8};

byte colPins[COLS] = {9, 10, 11, 12};



Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);



const String PASSWORD = "1234";  // 预设密码

String inputCode = "";

const int RED_LED = 3;

const int GREEN_LED = 4;

const int BUZZER = 2;



void setup() {

  pinMode(RED_LED, OUTPUT);

  pinMode(GREEN_LED, OUTPUT);

  pinMode(BUZZER, OUTPUT);

  Serial.begin(9600);

  Serial.println("密码锁就绪，请输入 4 位密码...");

}



void loop() {

  char key = keypad.getKey();

  if (key) {

    Serial.print("*");  // 不显示按键内容



    if (key == '#') {  // # 键确认

      if (inputCode == PASSWORD) {

        digitalWrite(GREEN_LED, HIGH); delay(2000);

        digitalWrite(GREEN_LED, LOW);

        Serial.println("\n✅ 密码正确，已开锁！");

      } else {

        digitalWrite(RED_LED, HIGH);

        tone(BUZZER, 500, 1000);

        digitalWrite(RED_LED, LOW);

        Serial.println("\n❌ 密码错误！");

      }

      inputCode = "";

    }

    else if (key == '*') {  // * 键清除

      inputCode = "";

      Serial.println("\n输入已清除");

    }

    else {

      inputCode += key;

      if (inputCode.length() > 4) inputCode = inputCode.substring(0,4);

    }

  }

}

```



## 教学重点

- 矩阵键盘节省 IO（4×4 只需 8 个 IO 而非 16 个）

- `Keypad` 库封装了行扫描逻辑，调用 `getKey()` 即可获取按键

- 密码校验用 `String` 比较，注意内存管理

- `#` 确认、`*` 清除 是常见的交互约定



## 常见错误

- 行/列引脚定义与接线不一致

- `getKey()` 非阻塞，不按时返回 `NO_KEY`

- 使用 `==` 比较 char 数组而非 `String` 对象

- 连续按键时 `inputCode` 没做长度限制导致溢出

