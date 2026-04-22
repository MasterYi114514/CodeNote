
# 1. 基础语法

Lambda表达式用于定义匿名函数，语法如下：

```
[捕获的变量](参数的列表) 限定符->返回类型       // 限定符和返回类型可以省略
{
    // 具体的函数体
    // 函数体中可以调用捕获的变量与参数列表中的变量
}
```

例如：
```cpp
auto add = [](int x, int y) -> int
{
    return x + y;
}

std::sort(vec.begin(), vec.end(), [](int a, int b) { return a < b; });
```

注：
1. 可以在Lambda表达式后加上参数列表来直接调用Lambda表达式，例如：
```cpp
void func()
{
    int result = [](int x, int y) { return x + y; }(1, 2);  // 直接调用Lambda表达式，传入参数1和2
    std::cout << result;  // 输出3
}
```

2. 当参数列表为空时，可以省略参数列表中的括号，例如：
```cpp
void func()
{
    auto lambda1 = [] { return 42; };       // 省略参数列表中的括号
    std::cout << lambda1() << std::endl;  // 输出42

    int result = []() { return 42; }();     // 直接调用Lambda表达式，传入空参数列表
    std::cout << result << std::endl;  // 输出42
}
```

# 2. 捕获变量

Lambda表达式可以捕获外部变量，捕获方式有以下几种：
1. 值捕获：通过值捕获的变量在Lambda表达式中是只读的，无法修改。
2. 引用捕获：通过引用捕获的变量在Lambda表达式中是可读写的，可以修改。
3. 隐式捕获：可以使用`[=]`表示捕获所有外部变量的值，使用`[&]`表示捕获所有外部变量的引用。

## 2.1 值捕获

值捕获的前提是变量可以拷贝，同时被捕获的变量在Lambda表达式被创建时拷贝。
```cpp
void func()
{
    int value = 1;
    auto lambda = [value]()
    {
        // value = 1;           // 错误的写法，value在lambda内部是只读的
        return value;
    }

    value = 2;                  // 修改value的值，不会影响lambda内部的value
    std::cout << lambda();      // 输出1
}
```

注：
1. 值捕获的变量在Lambda表达式被创建时拷贝，因此即使外部变量的值发生了改变，Lambda表达式内部的变量值也不会改变。
2. 可以给Lambda函数加上`mutable`限定符，使得值捕获的变量在Lambda表达式内部变为可读写的：
```cpp
void func()
{
    int value = 1;
    auto lambda = [value]() mutable
    {
        value = 2;              // 这里修改了lambda内部的value，不会影响外部的value
        return value;
    }

    std::cout << lambda();      // 输出2
    std::cout << value;         // 输出1，外部的value没有被修改
}
```

## 2.2 引用捕获

引用捕获的前提是变量必须存在于Lambda表达式的作用域内，并且在Lambda表达式被调用时仍然有效。
```cpp
void func()
{
    int value = 1;
    auto lambda = [&value]()
    {
        return value;           // 这里捕获了value的引用，lambda内部可以修改value
    }

    value = 2;                  // 修改value的值，会影响lambda内部的value
    std::cout << lambda();      // 输出2
}
```

## 2.3 隐式捕获

隐式捕获可以使用`[=]`表示捕获所有外部变量的值，使用`[&]`表示捕获所有外部变量的引用，编译器会自动捕获Lambda表达式中使用到的外部变量。
```cpp
void func()
{
    int x = 1, y = 2;
    auto lambda1 = [=]()
    {
        return x + y;           // 捕获了x和y的值，lambda1内部无法修改x和y
    }

    auto lambda2 = [&]()
    {
        x = 3;                  // 修改了x的值，lambda2内部可以修改x和y
        return x + y;           // 捕获了x和y的引用，lambda2内部可以修改x和y
    }
}
```

这里的写法等效于:
```cpp
auto lambda1 = [x, y]()
{
    return x + y;
}

auto lambda2 = [&x, &y]()
{
    x = 3;
    return x + y;
}
```

也可以对默认捕获添加特例，如：
```cpp
void func()
{
    int x = 1, y = 2, z = 3;
    auto lambda = [=, &z]()
    {
        // 捕获了x和y的值，捕获了z的引用
        z += 1;                     // 修改了z的值
        return x + y + z;           // lambda内部无法修改x和y，但可以修改z
    }
}
```

类似地 `[&, z]` 的写法也是正确的。

## 2.4 捕获this指针

Lambda表达式可以捕获this指针，允许在Lambda表达式中访问类的成员变量和成员函数。
```cpp
class Example
{
public:
    Example() = default;

    void func()
    {
        auto lambda1 = [this]()
        {
            // 这里捕获了this指针，可以访问类的成员变量和成员函数，相当于引用捕获
            std::cout << memberVariable << std::endl;
            memberFunction();
        }

        auto lambda2 = [*this]()
        {
            // 这里捕获了*this，即当前对象的副本，可以访问类的成员变量和成员函数，相当于值捕获
            std::cout << memberVariable << std::endl;
            memberFunction();
        }

        lambda1();
        lambda2();
    }

private:
    int memberVariable = 42;
};
```

## 2.5 不需要捕获的变量

对于全局变量和静态变量，Lambda表达式不需要捕获就可以直接访问。
```cpp
int globalVariable = 10;

void func()
{
    static int staticVariable = 20;

    auto lambda = []
    {
        // 直接访问全局变量和静态变量，无需捕获
        std::cout << globalVariable << std::endl;   // 输出10
        std::cout << staticVariable << std::endl;   // 输出20
    }

    lambda();
}
```

# 3. 泛型Lambda表达式

C++14引入了泛型Lambda表达式，允许在Lambda表达式的参数列表中使用auto关键字来表示参数类型，这样Lambda表达式就可以接受不同类型的参数。
```cpp
auto genericLambda = [](auto a, auto b)
{
    return a + b;  // 这里的a和b可以是任何类型，只要支持+运算符
};

int result1 = genericLambda(1, 2);           // result1的值为3
double result2 = genericLambda(1.5, 2.5);    // result2的值为4.0
```
