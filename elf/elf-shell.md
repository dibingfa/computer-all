file a.out
```sh
# gcc a.c
a.out: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=a00dd2efbcbd8e23589373d89189b9785f0b7542, not stripped
# gcc -static a.c (使用 yum install glibc-static 安装 libc.a)
a.out: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked, for GNU/Linux 2.6.32, BuildID[sha1]=df8e45dac709f52e687727ddcc19e93cbac022d9, not stripped
```


nm hello.o
```
[root@VM-24-11-centos hello]# nm hello.o
0000000000000000 T hello
[root@VM-24-11-centos hello]# nm main.o
                 U hello
0000000000000000 T main
```

反汇编 objdump -d main.o
```
[root@VM-24-11-centos hello]# objdump -d main.o
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
