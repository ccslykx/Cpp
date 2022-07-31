### 浮点数输出控制

#### 设置小数位数输出

1. 包括头文件 `#include <iomanip>`
2. `cout << fixed << setprecision(2) << floatValue << endl;` 其中`fixed`表示即使小数为部分为`0`也仍然输出相应长度的小数，`setprecision(2)`表示保留2位小数。也可用`setiosflags(ios::fixed)`表示。