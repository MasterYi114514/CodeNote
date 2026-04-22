
# 1. const 与 constexpr 的区别

## 1.1 运行时变量、常量和编译期常量

`const` 关键字用于声明一个变量为常量，意味着它的值在初始化后不能被修改。然而，`const` 变量的值可以在运行时确定，这意味着它们不一定是编译期常量。 
`constexpr` 关键字用于声明一个变量为编译期常量，意味着它的值必须在编译时确定，并且在程序运行时不可修改。`constexpr` 变量必须被初始化为一个常量表达式。

**编译期常量**：在编译阶段就能确定其值的常量。编译器在编译时会计算这些常量的值，并将其直接嵌入到生成的代码中。

```cpp
int size1 = 10;
const int size2 = 20;
constexpr int size3 = 30;

int arr1[size1];    // 错误：size1 不是编译期常量
int arr2[size2];    // 正确：size2 是 const 常量
int arr3[size3];    // 正确：size3 是 constexpr 常量
```

## 1.2 constexpr 用于修饰函数

`constexpr` 还可以用于修饰函数，表示该函数可以在编译时求值。这样的函数必须满足以下条件：
- 函数体必须包含一个单一的 `return` 语句。
- 函数的参数和返回类型必须是字面类型（literal type）。

```cpp
constexpr int fibonacci(const int n)
{
    return (n <= 1) ? n : (fibonacci(n - 1) + fibonacci(n - 2));
}

int main()
{
    constexpr int result = fibonacci(10);       // 在编译时计算斐波那契数列的第10项
    return 0;
}
```

如果这里的 `fibonacci` 函数未被声明为 `constexpr`，则无法用其为 `constexpr int` 类型的 result 进行初始化，因为编译器无法在编译时计算其值。

## 1.3 `if constexpr` 的使用

`if constexpr` 是 C++17 引入的一种编译时条件语句，它允许在编译时根据条件选择性地编译代码块。与普通的 `if` 语句不同，`if constexpr` 中的条件必须是一个编译时常量表达式。

```cpp
template<typename T>
void printTypeInfo()
{
    if constexpr (std::is_integral_v<T>) 
    {
        std::cout << "T is an integral type." << std::endl;
    } 
    else if constexpr (std::is_floating_point_v<T>) 
    {
        std::cout << "T is a floating-point type." << std::endl;
    } 
    else
    {
        std::cout << "T is some other type." << std::endl;
    }
}
```

# 2. `const` 关键字的使用事项

## 2.1 常量与变量的区别

常量是不可以修改的，而变量是可以修改的。
常量在定义时必须初始化，并且在程序运行过程中保持不变。
```cpp
const int MAX_SIZE = 100;  // MAX_SIZE 是一个常量，必须在定义时初始化
int size = 50;             // size 是一个变量，可以在程序运行过程中修改

size = 75;                 // 正确：可以修改变量 size
MAX_SIZE = 200;           // 错误：不能修改常量 MAX_SIZE
```

## 2.2 `指针/引用` 与 `const` 的关系

### 2.2.1 `指向常量的引用`：引用指向的内容是常量，不能通过该引用修改内容。

```cpp
int value1 = 10, value2 = 20;

const int& ref1 = value1;  // ref1 是一个指向常量的引用
ref1 = 30;                // 错误：不能修改 ref1 指向的内容
ref1 = value2;            // 错误：不能修改 ref1 指向的内容
```

目前来看，指向常量的引用并无什么作用。

### 2.2.2 `常量指针` 和 `指向常量的指针`

**常量指针**：指针本身是常量，指向的地址不能改变，但指向的内容可以修改。

```cpp
int value = 10, anotherValue = 20;
int* const ptr1 = &value;   // ptr1 是一个常量指针

*ptr1 = 20;                // 正确：可以修改 ptr1 指向的内容
ptr1 = &anotherValue;      // 错误：不能修改 ptr1 指向的地址
```

**指向常量的指针**：指针指向的内容是常量，不能通过该指针修改内容，但指针本身可以改变指向。

```cpp
int value = 10, anotherValue = 20;
const int* ptr2 = &value;  // ptr2 是一个指向常量的指针
```

### 2.2.3 使用 `指针/引用` 时对象的访问权限

我们可以通过 `指向常量的指针` 来访问常量和变量，但不能通过它修改常量的值。
```cpp
int value1 = 10;
const int value2 = 20;

const int* ptr1 = &value1;  // ptr1 是一个指向常量的指针
std::cout << *ptr1 << std::endl;    // 正确：可以访问 ptr1 指向的内容
*ptr1 = 20;                // 错误：不能修改 ptr1 指向的内容

const int* ptr2 = &value2;  // ptr2 是一个指向常量的指针
std::cout << *ptr2 << std::endl;    // 正确：可以访问 ptr2 指向的内容
*ptr2 = 30;                // 错误：不能修改 ptr2 指向的内容
```

但是，不可以通过普通的指针来访问常量，因为指针的解引用可能会修改常量的值。
```cpp
const int value = 10;
int *ptr = &value;          // 错误：不能将 const int* 转换为 int*
```

因此，以下操作在C语言中可行，但在C++中会导致编译错误：
```cpp
char* str = "Hello";        // "Hello" 是字符串字面量，类型为 const char*，不能赋值给 char*
```

但是声明字符数组的写法仍旧可行：
```cpp
char str[] = "Hello";        // 声明一个字符数组并初始化
```

这里实际上是创建了一个字符数组，并将字符串字面量 "Hello" 的内容复制到该数组中。虽然字符串字面量(`"Hello"`)仍旧是不可修改的，但通过字符数组 str 已经存储了它的备份。