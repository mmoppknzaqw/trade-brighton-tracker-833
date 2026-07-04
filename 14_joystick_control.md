# Arduino：摇杆控制 XY 坐标



## 说明

用 PS2 摇杆模块模拟游戏手柄，读取 X/Y 轴模拟值和按钮按下状态。学习双轴模拟输入、摇杆中位校准、自定义映射。



## 硬件需求

- Arduino UNO ×1

- PS2 摇杆模块 ×1



## 电路连接

| 摇杆 | Arduino |

|------|---------|

| VRx | A0 |

| VRy | A1 |

| SW (按键) | D2 |

| VCC | 5V |

| GND | GND |



## 代码

```cpp

const int PIN_VRX = A0;

const int PIN_VRY = A1;

const int PIN_SW  = 2;



// 中位值（摇杆静止时的读数，需校准）

const int CENTER_X = 512;

const int CENTER_Y = 512;

const int DEAD_ZONE = 30;  // 死区，忽略小幅抖动



void setup() {

  pinMode(PIN_SW, INPUT_PULLUP);

  Serial.begin(9600);

  Serial.println("摇杆坐标读取...");

}



void loop() {

  int rawX = analogRead(PIN_VRX);

  int rawY = analogRead(PIN_VRY);

  bool btnPressed = !digitalRead(PIN_SW);  // LOW = 按下



  // 映射到 -100 ~ 100 的范围

  int x = map(rawX, 0, 1023, -100, 100);

  int y = map(rawY, 0, 1023, -100, 100);



  // 死区处理：中心附近视为 0

  if (abs(x) < DEAD_ZONE * 100 / 512) x = 0;

  if (abs(y) < DEAD_ZONE * 100 / 512) y = 0;



  Serial.print("X: ");

  Serial.print(x);

  Serial.print("  Y: ");

  Serial.print(y);

  Serial.print("  Btn: ");

  Serial.println(btnPressed ? "按下" : "松开");



  delay(100);

}

```



## 教学重点

- 摇杆是两个电位器正交排列 + 一个轻触开关

- **死区（Dead Zone）**概念：消除机械回中不精确带来的噪声

- `map()` 灵活映射范围（可以反向映射）

- 游戏开发中摇杆输入处理的基础



## 常见错误

- XY 轴和数据对应关系接反 → 上下左右颠倒

- 不设死区 → 静止状态下漂移

- 忘记绝对值判断 `abs()` → 只有正方向死区有效

