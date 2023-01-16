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
int main(int argc, char* argv[]) {
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

### 2.3.3 编译的各个阶段

1. **预处理阶段**

   gcc编译器提供了一种模式，可以仅仅对输入文件执行预处理操作：

   ```sh
   gcc -E <input file> -o <output preprocessed file>.i 
   gcc -E -P <input file> -o <output preprocessed file>.i 
   ```

2. **语言分析阶段**

   - **词法分析**：将要代码分割成不可分割的单词。
   - **语法分析**：将提取处理的单词连接成单词序列，并根据编程语言规则验证其顺序是否合理。
   - **语义分析**：目的是发现符合语法规则的语句是否具有实际意义。比如，将两个整数相加并把结果赋值给一个对象的语句，虽然通过语法规则的检查，但可能无法通过语义检查（除非该对象重载了赋值操作符）。

3. **汇编阶段**

   在这个阶段中，编译器会将标准的语言集合转换成特定CPU指令集的语言集合。

   gcc编译器支持一种模式：将输入文件的源代码转换对应ASCII编码的文本文件，其中包含了特定芯片或操作系统所对应汇编指令集的代码行（默认格式：AT&T）。

   ```sh
   gcc -S <input file> -o <output assembler file>.s
   # AT&T格式
   gcc -S -masm=att <input file> -o <output assembler file>.s 
   # Intel 格式
   gcc -S -masm=intel <input file> -o <output assembler file>.s
   ```

4. **优化**

   当由源代码文件生成最初版本的汇编代码后，优化过程就开始了，这可以将程序的寄存机使用率最小化

5. **代码生成阶段**

   gcc编译器可以用于完整的编译（其中包括预处理、汇编呵呵编译阶段），该过程中会生成二进制目标文件（标准扩展名是.o），其结构遵循ELF格式规范。

   ```sh
   gcc -c <input file> -o <output file>.o
   ```

   objdump（在常用的binutils包中）是一款专门用于反汇编二进制文件的Linux工具，除此之外它还有很多其他功能。该工具不仅能转换平台的二进制机器指令序列，还会指出指令所在的地址。

   ```sh
   objdump -D <input file>.o
   objdump -D -M att<input file>.o
   objdump -D -M intel <input file>.o
   ```

### 2.3.4 目标文件属性

概括来讲：

- 目标文件是通过其对应的源代码翻译得到。由于一个项目中有许多的源代码文件，因此编译的结果将是一组目标文件。

  当编译完成后，后续的构建过程将基于目标文件进行。

- 符号（symbol）和节（section）是目标文件的基本组成部分，其中符号表示的是程序中的内存地址或数据内存。

  绝大多数的目标文件中都包含代码节（.text）、初始化数据节（.data）、未初始化数据节（.bss）以及一些特殊节（比如调试信息等）。

- 构建程序的目的在于：将编译的每个独立的源代码文件生成的节拼接到一个二进制可执行文件中。

  最终生成的二进制文件中包含了多个相同类型的节（.text、.data和.bss节等），而这些节是从每个独立的目标文件中拼接得到的。

- 目标文件中独立的节都有可能包含在最终的程序内存映射中，因此目标文件中每个节的起始地址都临时设置成0，等待链接时调整。

  在程序构建过程的后续阶段（链接阶段）中会确定程序内存映射中每个独立节的实际地址范围。

- 在将目标文件的节拼接到程序内存映射的过程中，其中唯一重要的参数是节长度，准确地说是节的地址范围。

- 目标文件中不包含专门的节会影响堆与栈中的数据。内存映射中的堆与栈内容完全在运行时确定，除了需要指定堆与栈的默认长度以外，并不需要程序指定任何其他初始化 

- 目标文件只包含了程序.bss



### 2.3.5 编译过程的局限性



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