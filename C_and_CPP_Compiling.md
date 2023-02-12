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

- 目标文件中不包含专门的节会影响堆与栈中的数据。内存映射中的堆与栈内容完全在运行时确定，除了需要指定堆与栈的默认长度以外，并不需要程序指定任何其他初始化 。

- 目标文件只包含了程序.bss（未初始化数据）节的基本信息，而.bss节本身也仅仅只有字节长度信息。转载器会利用这些有限的数据为.bss节建立足够其数据存储的内存空间。

二进制格式规则的设计通常是为了支持C/C++语言结构并帮助其解决实现问题。

在Linux上，可执行和可链接（Executable and Linkable Format，ELF）已经得到了普遍应用。而在Windows上，二进制文件通常遵循PE/COFF格式规范。

### 2.3.5 编译过程的局限性

## 2.4 链接

### 2.4.1 链接定位

#### 1. 重定位

#### 2. 解析引用

#### 3. 演示项目：链接示例

```sh
# 编译项目
gcc -c function.c main.c
gcc function.o main.o -o demoApp
# 反汇编
objdump -D -M intel main.o
objdump -D -M intel demoApp
# 执行下面这条命令，可以对含有未初始化数据的.bss节进行反汇编操作
objdump -x -j .bss demoApp
```

### 2.4.2 链接器视角

# 第三章 加装程序执行阶段

## 3.1 shell 的重要性

## 3.2 内核的作用

## 3.3 装载器的作用

### 3.3.1 装载器视角下的二进制文件（节与段）

我们使用了readelf工具来检查段，可以看到多个不同的链接器被分组合并成装载器段。

### 3.3.2 程序加载阶段

```sh
readelf --segments libmreloc.so
```

## 3.4 程序执行入口点

#### 3.4.1 装载器查找入口点

#### 3.4.2 _start() 函数的作用

#### 3.4.3 __libc_start_main() 函数的作用

#### 3.4.4 栈和调用惯例

# 第四章 重要概念的作用

# 4.1 静态库



# 4.2 动态库

### 4.2.1 动态库和共享库

### 4.2.2 动态库链接详解

### 4.2.3 Windows平台时中动态链接的特点

### 4.2.4 动态库的特点

### 4.2.5 引用程序二进制接口（ABI）

## 4.3 静态库和动态库对比

### 4.3.1 导入选择条件的差异

#### 1. 静态库的导入选择条件

#### 2. 动态库的导入选择条件

#### 3. 导入完整归档的情况

```sh
gcc -fPIC <source files> -Wl,--whole-archive -l<static libraries> -o <shlib filename>
gcc -fPIC <source files> -o <execuable-output-file> -Wl,--whole-archive -l<libraries-to-be-entirely-linked-in> -Wl,--no-whole-archive -l<all-other-libraries> 
```

### 4.3.2 部署难题

## 4.4 一些有用的类比 

# 第五章 使用静态库

## 5.1 创建静态库

### 5.1.1 创建 Linux 静态库

```sh
gcc -c first.c second.c
ar rcs libstaticlib.a first.o second.o
```

### 5.1.2 创建 Windows静态库

## 5.2 使用静态库

## 5.3 静态库设计技巧

### 5.3.1 丢失符号可见性和唯一性的可能性

### 5.3.2 静态库使用禁忌

### 5.3.3 静态库链接的具体规则

### 5.3.4 将静态库转成动态库

- 使用打包工具（ar）来提取所以静态库的目标文件，如：

  ```sh
  ar -x <static library>.a
  ```

- 链接器使用提取出来的目标文件集合构建动态库

### 5.3.5 静态库在64位Linux平台上的问题

# 第六章 设计动态链接库：基础篇

## 6.1 创建动态链接库

### 6.1.1 在Linux中创建动态库

通常来说，构建动态库的过程至少需要下面两个选项：

- -fPIC 编译器选项
- -shared 链接器选项

下面简单示例演示了从两个源代码文件创建动态库的过程：

```sh
gcc -fPIC -c first.c second.c
gcc -shared first.o second.o -o libdynamiclib.so
```

### 6.1.2 在Windows中创建动态库

## 6.2 设计动态库

### 6.2.1 设计二进制接口

### 6.2.2 设计应用程序的二进制接口

### 6.2.3 控制动态库符号的可见性

#### 1. 导出Linux动态库符号

**在构建过程中控制符号的可见性**

方法1：（影响所有代码）

```sh
-fvisibility=hidden
```

方法2：（只影响单个符号）

```cpp
__attribute__((visibility("<default | hidden>")))
```

方法3：（影响单个符号或者一组符号）

```cpp
// 该选项通常用在头文件中
#pragma visibility push(hidden)
void someprivatefunction_1(void);
void someprivatefunction_2(void);
// ...
void someprivatefunction_N(void);
#pragma visibility pop
```

#### 2. 演示示例：控制符号的可见性

##### 使用 strip 工具

```sh

```





#### 3. 导出Windows动态链接库符号



### 6.2.4 完成链接需要满足的条件



## 6.3动态链接模式

### 6.3. 加载时动态链接

### 6.3. 运行时动态链接

### 6.3. 比较两种动态库链接模式



# 第七章 定位库文件

# 第八章 动态库的设计：进阶篇

# 第九章 动态链接时的重复符号处理

# 第十章 动态库的版本控制

# 第十一章 动态库：其他主题

# 第十二章 Linux 工具集

# 第十三章 平台实践

# 第十四章 Windows 工具集