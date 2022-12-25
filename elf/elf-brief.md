hello.c
```c
#include <stdio.h>
#define STR "hello world\n"
void main() {
  printf(STR);
}
```

## gcc proc
https://gcc.gnu.org/onlinedocs/gcc/Overall-Options.html#Overall-Options

预处理（preprocess）：头文件展开、宏定义替换、条件编译去除
```shell
gcc -E hello.c -o hello.i
```
编译（compile）：转换成汇编语言
```shell
gcc -S hello.i -o hello.s
```

汇编（assemble）：转换成 ELF relocatable
```
gcc -c hello.s -o hello.o
```

链接（link）：符号解析
```
# 动态链接
gcc hello.o -o hello
# 静态链接
gcc hello.o -o hello -static
```

## ELF type

relocatable 可重定位文件：.o .a

executable 可执行文件：无扩展名

shared object 共享目标文件：.so

core dump 核心转储文件：core dump

## example



## doc
https://sourceware.org/binutils/docs-2.39/ld.html
