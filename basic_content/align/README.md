# 对齐
什么是内存对齐？为什么要内存对齐？怎么对齐？

内存对齐
对齐规则：
1. 按照成员的声明顺序，依次安排内存，其偏移量为成员大小的整数倍；
2. 0看做任何成员的整数倍，最后结构体的大小为最大成员的整数倍

为什么要内存对齐？
1. 平台原因(移植原因)：不是所有的硬件平台都能访问任意地址上的任意数据的；某些硬件平台只能在某些地址处取某些特定类型的数据，否则抛出硬件异常。
2. 性能原因：数据结构(尤其是栈)应该尽可能地在自然边界上对齐。原因在于，为了访问未对齐的内存，处理器需要作两次内存访问；而对齐的内存访问仅需要一次访问。

```c
#include <stdio.h>
// 求结构体大小
// 1. 先计算各自的offset，然后得出总字节数total
// 0~n1
// n1+1 < s2 n2 = s2
// n1+1 > s2 (n1+1) % (s2)==0 ? n2=n1+1: n2=((n1+1)/s2 + 1) * s2

// 2. 总字节数是否等于最大成员字节数的整数倍
// ret = total % max(member)
// if ret == 0: total
// else ret = (total/max(member) + 1) * max(member)
typedef struct {
  int a;    // 4->off:0~3
  double b; // 8->off:8~15
  short c;  // 2->off:16~17
} A;

typedef struct {
  int a;    // 0~3
  short b;  // 4~5
  double c; // 8~15
} B;

int x __attribute__((aligned(16))) = 0;

// #pragma pack(1)
#pragma pack(push, 1)
struct foo {
  char a; //0
  int b; //4~7
  float c; // 8~15
  double d; // 16~23
  int *pa; // 24~31
  char *pc; // 32~39 
  short e; // 40~41
};//42 --> (42/8+1)*8=48
#pragma pack(pop)

int main() {
  int s1 = sizeof(A);
  int s2 = sizeof(B);
  printf("%d, %d\n", s1, s2); // 24, 16
  printf("%d\n", sizeof(struct foo));
  return 0;
}
```