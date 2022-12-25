## 一、静态链接的计算过程

### main.c
```c
void hello();
void main() {
  hello();
}
```

### hello.c
```c
#include <stdio.h>
#include <stdint.h>
int main();
void hello(){
  printf("main=%X\n", main);
}
```

### gcc -c main.c && objdump -d main.o
```
[root@VM-24-11-centos hello]# gcc -c main.c && objdump -d main.o
main.o:     file format elf64-x86-64
Disassembly of section .text:
0000000000000000 <main>:
   0:   55                      push   %rbp
   1:   48 89 e5                mov    %rsp,%rbp
   4:   b8 00 00 00 00          mov    $0x0,%eax
   9:   e8 00 00 00 00          callq  e <main+0xe>
   e:   5d                      pop    %rbp
   f:   c3                      retq
```
注意到这里的 callq hello 是 000000

### gcc -c hello.c && objdump -d hello.o
```
[root@VM-24-11-centos hello]# gcc -c hello.c && objdump -d hello.o
hello.o:     file format elf64-x86-64
Disassembly of section .text:
0000000000000000 <hello>:
   0:   55                      push   %rbp
   1:   48 89 e5                mov    %rsp,%rbp
   4:   be 00 00 00 00          mov    $0x0,%esi
   9:   bf 00 00 00 00          mov    $0x0,%edi
   e:   b8 00 00 00 00          mov    $0x0,%eax
  13:   e8 00 00 00 00          callq  18 <hello+0x18>
  18:   5d                      pop    %rbp
  19:   c3                      retq
```

### gcc main.c hello.c && objdump -d a.out
```
000000000040052d <main>:
  40052d:       55                      push   %rbp
  40052e:       48 89 e5                mov    %rsp,%rbp
  400531:       b8 00 00 00 00          mov    $0x0,%eax
  400536:       e8 02 00 00 00          callq  40053d <hello>
  40053b:       5d                      pop    %rbp
  40053c:       c3                      retq   
000000000040053d <hello>:
  40053d:       55                      push   %rbp
  40053e:       48 89 e5                mov    %rsp,%rbp
  400541:       be 2d 05 40 00          mov    $0x40052d,%esi
  400546:       bf f0 05 40 00          mov    $0x4005f0,%edi
  40054b:       b8 00 00 00 00          mov    $0x0,%eax
  400550:       e8 bb fe ff ff          callq  400410 <printf@plt>
  400555:       5d                      pop    %rbp
  400556:       c3                      retq   
  400557:       66 0f 1f 84 00 00 00    nopw   0x0(%rax,%rax,1)
  40055e:       00 00 
```
注意地址

- main: 40052d
- hello: 40053d
- main callq hello: 02


### ./a.out
```
main=40052D
```

### hello 的地址可以计算出来 hello = main + callq 指令偏移量 + 静态符号解析后的值
hello.c
```c
#include <stdio.h>
#include <stdint.h>
int main();
void hello(){
  char *p = (char *)main + 0x9 + 1;
  int callqp = *(int*)p;
  int callqoffset = 0xe;
  printf("callqp=%x\n", callqp);
  printf("mainoffset=%x\n", main);
  printf("callqoffset=%x\n", callqoffset);
  printf("hellooffset=%x\n", hello);
  printf("calculate hellooffset=%x\n", main + callqoffset + callqp);
}
```
gcc main.c hello.c && ./a.out
```
callqp=2
mainoffset=40052d
callqoffset=e
hellooffset=40053d
calculate hellooffset=40053d
```

## 二、静态链接的符号表