
需要预先学习：
- [[union（联合）]]
- [[类和对象]]

# `std::variant` 的基本用法

`std::variant` 是 C++17 引入的一个类型安全的联合体（type-safe union），它可以存储多种类型中的一种，并且在访问时会进行类型检查。
```cpp
#include <iostream>
#include <variant>

int main() {
    std::variant<int, float, std::string> myVariant;

    // 存储整数
    myVariant = 42;
    std::cout << "Integer: " << std::get<int>(myVariant) << std::endl;

    // 存储浮点数
    myVariant = 3.14f;
    std::cout << "Float: " << std::get<float>(myVariant) << std::endl;

    // 存储字符串
    myVariant = "Hello, World!";
    std::cout << "String: " << std::get<std::string>(myVariant) << std::endl;

    return 0;
}
```

## `std::bad_variant_access` 异常

当访问 `std::variant` 中的值时，如果类型不匹配，会抛出 `std::bad_variant_access` 异常：
```cpp
std::variant<int, float> myVariant = 42;

try
{
    std::cout << std::get<float>(myVariant) << std::endl; // 这里会抛出异常
} 
catch(const std::bad_variant_access& e) 
{
    std::cerr << "Error: " << e.what() << std::endl;
}
```

## `variant.index()` 方法 与 `holds_alternative` 函数

`variant.index()` 方法返回当前存储的类型在 `std::variant` 中的索引位置，而 `std::holds_alternative<T>(v)` 函数用于检查 `std::variant` 是否存储了类型 `T` 的值。

```cpp
std::variant<int, float> myVariant = 42;

std::cout << "Index: " << myVariant.index() << std::endl;   // 输出: Index: 0

// 可以借此判断 myVariant 当前存储的类型
if(myVariant.index() == 0)
{
    std::cout << "Holds int" << std::endl;
}
else // myVariant.index() == 1
{
    std::cout << "Holds float" << std::endl;
}

if (std::holds_alternative<int>(myVariant)) 
{
    std::cout << "Holds int" << std::endl;
}
else    
{
    std::cout << "Does not hold int" << std::endl;
}
```

# `std::visit` 

`std::visit` 是 C++17 引入的一个函数，用于对 `std::variant` 中的值进行访问。它接受一个访问器（visitor）和一个 `std::variant` 对象作为参数，并根据当前存储的类型调用相应的函数。

```cpp
#include <iostream>
#include <variant>

// 定义 visitor 访问器
struct Visitor
{
    void operator()(int i) const
    {
        std::cout << "Integer: " << i << std::endl;
    }

    void operator()(float f) const
    {
        std::cout << "Float: " << f << std::endl;
    }

    void operator()(const std::string& s) const
    {
        std::cout << "String: " << s << std::endl;
    }
};

int main()
{
    std::variant<int, float, std::string> myVariant;

    myVariant = 42;
    std::visit(Visitor{}, myVariant);   // 输出: Integer: 42

    myVariant = 3.14f;
    std::visit(Visitor{}, myVariant);   // 输出: Float: 3.14

    myVariant = "Hello, World!";
    std::visit(Visitor{}, myVariant);   // 输出: String: Hello, World!

    return 0;
}
```

## `overload` 模式

在 C++17 中，可以使用 `std::overload` 模式来简化访问器的定义。`std::overload` 是一个辅助函数模板，可以将多个函数对象组合成一个访问器。

```cpp
auto visitor = std::overload(
    [](int i) { std::cout << "Integer: " << i << std::endl; },
    [](float f) { std::cout << "Float: " << f << std::endl; },
    [](const std::string& s) { std::cout << "String: " << s << std::endl; }
);

std::variant<int, float, std::string> myVariant = 42;

int main()
{
    std::visit(visitor, myVariant);   // 输出: Integer: 42

    return 0;
}
```