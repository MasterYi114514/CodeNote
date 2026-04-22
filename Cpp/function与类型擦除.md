
需要预先学习：
- [[函数进阶]]

# 1. `std::function`

`std::function` 是 C++11 引入的一个模板类，用于封装可调用对象（如函数指针、函数对象、lambda 表达式等）。它提供了一种统一的方式来存储和调用这些可调用对象。

```cpp
#include <iostream>
#include <functional>

void functionExample() 
{
    std::cout << "Hello from a function!" << std::endl;
}

int main()
{
    std::function<void()> func = functionExample; // 将函数指针赋值给 std::function
    func(); // 调用函数
    return 0;
}
```

注：
1. `std::function` 可以存储任何可调用对象，只要它们的签名匹配。
   - `签名` 指的是函数的参数类型和返回类型。例如，`std::function<void()>` 表示一个不接受参数且返回 `void` 的可调用对象。
2. `std::function` 内部使用类型擦除（type erasure）技术来实现对不同类型可调用对象的统一处理。这使得它非常灵活，但也可能会带来一些性能开销。
   - `类型擦除` 是一种编程技术，允许在运行时隐藏对象的具体类型，从而实现对不同类型对象的统一处理。


# 2. `function` 的实现

```cpp
#pragma once

#include <memory>

namespace YY
{
    /*
        抽象接口类，定义了一个纯虚函数 invoke()，用于调用可调用对象。
    */
    template<typename R, typename... Args>
    struct ICallable
    {
        virtual R invoke(Args&& ... args) = 0;     // 纯虚函数，定义调用接口
        virtual ~ICallable() = default;             // 虚析构函数，确保正确释放资源
    };

    /*
        桥接类，继承自 ICallable，并持有一个可调用对象。
    */
    template<typename T, typename R, typename... Args>
    class ICallableImpl : public ICallable<R, Args...>
    {
        T callable;     // 存储可调用对象
    public:
        ICallableImpl(const T& c) : callable(std::move(c)) {}

        R invoke(Args&& ... args) override
        {
            return callable(std::forward<Args>(args)...);
        }
    };

    /*
        function 类，封装了一个 ICallable 对象，并提供了调用接口。
    */
    template<typename Signature>
    class function;

    template<typename R, typename... Args>
    class function<R(Args...)>
    {
        std::unique_ptr<ICallable<R, Args...>> funcPtr;

    public:
        template<typename T>
        function(T&& callable)
        {
            funcPtr = std::make_unique<ICallableImpl<T, R, Args...>>(std::forward<T>(callable));
        }

        R operator()(Args&& ... args) const
        {
            return funcPtr->invoke(std::forward<Args>(args)...);
        }
    };
} // namespace YY

```

可以进行如下测试：

```cpp
#include <iostream>
#include "function.hpp"

void func()
{
    std::cout << "Hello, World!" << std::endl;
}

int add(int a, int b)
{
    return a + b;
}

int main()
{
    YY::function<void()> f = func; // 创建一个 funtion 对象，封装了 func 函数
    f(); // 调用 funtion 对象，输出 "Hello, World!"

    YY::function<int(int, int)> addFunc = add; // 创建一个 funtion 对象，封装了 add 函数
    std::cout << "3 + 5 = " << addFunc(3, 5) << std::endl; // 调用 funtion 对象，输出 "3 + 5 = 8"
    return 0;
}
```