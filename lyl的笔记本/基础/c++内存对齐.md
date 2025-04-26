结构体内存对齐（如何计算结构体的大小）
 > 来自 <[https://blog.csdn.net/chenlong_cxy/article/details/114332324](https://blog.csdn.net/chenlong_cxy/article/details/114332324)>     
1. 第一个数据成员放在offset为0的地方，以后每个数据成员存储的起始位置要从该成员的大小或者成员的子成员大小的整数倍开始。
2. 结构体作为成员：结构体内部有结构体作为成员，该成员要从内部最宽的基本类型成员开始存储，double为8
3. 结构体的总大小，必须是其内部最大成员的整数倍
4. union，以size最大的元素为size
```cpp
union example
{    
    int a[5];    
    char b;    
    double c;  
};  
int result = sizeof(example);  
/* 如果以最长20字节为准，内部double占8字节，这段内存的地址0x00000020并不是double的整数倍，只有当最小为0x00000024时可以满足整除double（8Byte）同时又可以容纳int a[5]的大小，所以正确的结果应该是result=24 */  

struct example
{    
    int a[5];    
    char b;    
    double c;  
}test_struct;
int result = sizeof(test_struct);   /* 如果我们不考虑字节对齐，那么内存地址0x0021不是double（8Byte）的整数倍，所以需要字节对齐，那么此时满足是double（8Byte）的整数倍的最小整数是0x0024，说明此时char b对齐int扩充了三个字节。所以最后的结果是result=32 */  

struct example
{    
    char b;    
    double c;    
    int a;  
}test_struct;  
int result = sizeof(test_struct);   /* 字节对齐除了内存起始地址要是数据类型的整数倍以外，还要满足一个条件，那就是占用的内存空间大小需要是结构体中占用最大内存空间的类型的整数倍，所以20不是double（8Byte）的整数倍，我们还要扩充四个字节，最后的结果是result=24 */
```

|                         |         |         |                                                       |
| ----------------------- | ------- | ------- | ----------------------------------------------------- |
| 数据类型                    | 32位操作系统 | 64位操作系统 | 取值范围（32位）                                             |
| bool                    | 1bit    | 1bit    |                                                       |
| char                    | 1Byte   | 1Byte   | -128~127                                              |
| unsigned char           | 1Byte   | 1Byte   | 0~255                                                 |
| short int /short        | 2Byte   | 2Byte   | –32,768~32,767                                        |
| unsigned short          | 2Byte   | 2Byte   | 0~65,535                                              |
| int                     | 4Byte   | 4Byte   | -2,147,483,648~2,147,483,647                          |
| unsigned int            | 4Byte   | 4Byte   | 0~4,294,967,295                                       |
| long int /long          | 4Byte   | 8Byte   | –2,147,483,648~2,147,483,647                          |
| unsigned long           | 4Byte   | 8Byte   | 0~4,294,967,295                                       |
| long long int/long long | 8Byte   | 8Byte   | -9,223,372,036,854,775,808 ~9,223,372,036,854,775,807 |
| 指针                      | 4Byte   | 8Byte   |                                                       |
| float                   | 4Byte   | 4Byte   | 3.4E +/- 38 (7 digits)                                |
| double                  | 8Byte   | 8Byte   | 1.7E +/- 308 (15 digits)                              |
