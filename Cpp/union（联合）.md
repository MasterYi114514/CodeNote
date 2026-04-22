

# 联合的定义

联合（Union）是一种特殊的数据结构，它允许在同一内存位置存储不同类型的数据。
- 联合中的所有成员共享同一块内存，因此在任何时候只能使用其中的一个成员。
- 联合的大小取决于其最大成员的大小。

例如：
```c
union Data 
{
    int intValue;
    float floatValue;
    char charValue;
    char strValue[20];
};
```

在这个例子中，`Data` 联合可以存储一个整数、一个浮点数、一个字符或一个字符串，但只能同时存储其中的一个。

系统会根据联合中最大成员的大小来分配内存。如果 `int` 是 4 字节，`floa     t` 是 4 字节，`char` 是 1 字节，而 `strValue` 是 20 字节，那么 `Data` 联合的大小将是 20 字节。

# 联合的使用

```c
union Data data;
data.intValue = 10; // 存储整数
printf("Integer: %d\n", data.intValue);

data.floatValue = 3.14; // 存储浮点数
// 注意：此时 data.intValue 的值已经被覆盖了
printf("Float: %f\n", data.floatValue);
printf("Error Data: %d\n", data.intValue);

// 也可以用 {} 来初始化联合
union Data data2 = { 'A' };         // 初始化 charValue
union Data data3{ "Hello world" };  // 初始化 strValue
union Data data4 = { .intValue = 42 };      // 使用指定成员初始化
```