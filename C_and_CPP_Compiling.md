# 第一章 多任务操作系统基础

# 第二章 程序生命周期阶段基础

## 2.2 编写代码

```cpp
// function.h
// Created by zzj on 2022/11/12.
//

#pragma once

#define FIRST_OPTION
#ifdef FIRST_OPTION
#define MULTIPLIER (3.0)
#else
#define MULTIPLIER (2.0)
#endif

float add_and_multiply(float x, float y);
```

```cpp
// function.c
// Created by zzj on 2022/11/12.
//

#include "function.h"
int nCompletionStatus = 0;

float add(float x, float y){
    float z=x+y;
    return z;
}
float add_and_multiply(float x, float y){
    float z= add(x,y);
    z*=MULTIPLIER;
    return z;
}
```

```cpp
// main.c
// Created by zzj on 2022/11/12.
//

#include "function.h"
extern int nCompletionStatus=0;
int main() {
    float x=1.0;
    float y=5.0;
    float z;

    z= add_and_multiply(x,y);
    nCompletionStatus=1;
    return 0;
}
```





## 2.3 编译阶段

### 2.3.2 相关概念

- **编译：**从严格意义上讲，编译指的是将高级语言编写的源代码翻译成低级语言（通常是指汇编语言，甚至是机器代码）描述的代码过程。
- **交叉编译：**
- **反编译：**
- **语言编译：**是指将一种编译语言编写的源代码转换成另一种具有相同语言等级和复杂度的语言的过程
- **语言重写：**是指讲语言表达式转换成另一种形式，以更好适应特定任务的过程（比如优化）

### 2.3.3  

1. **预处理阶段**

   

2. **语言分析阶段**

   

3. **汇编阶段**

   

4. **优化**

   

5. **代码生成阶段**

   









# 第三章 加装程序执行阶段

# 第四章 重要概念的作用

# 第五章 使用静态库

# 第六章 设计动态链接库：基础篇

# 第七章 定位库文件

# 第八章 动态库的设计：进阶篇

# 第九章 动态链接时的重复符号处理

# 第十章 动态库的版本控制

# 第十一章 动态库：其他主题

# 第十二章 Linux 工具集

# 第十三章 平台实践

# 第十四章 Windows 工具集