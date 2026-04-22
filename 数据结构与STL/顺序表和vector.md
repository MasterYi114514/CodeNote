
建议预先学习：
- [[移动元素]]

需要预先学习：
- [[cpp内容总领]]

# 1. 顺序表的概念

## 1.1 线性表

n个具有相同特性的数据元素的有序序列。
```
 a1 --- a2 --- a3 --- a4 --- ... --- an
表头   a3的前驱      a3的后继          表尾
```

## 1.2 顺序表

用顺序存储实现的线性表

`**顺序存储**`：逻辑上相邻的元素，在内存中也放在相邻的位置。

# 2. 顺序表的模拟实现

以存储 `int` 类型数据为例。

实现方式：
- 采用动态申请的方式（ `new` 和 `delete`）
- 采用静态存储的方式

## 2.1 动态申请实现顺序表

```cpp
#pragma once

/*
    实现一个顺序表的基本操作，包括插入、删除、查找和遍历等功能。
*/

namespace YY
{
    template <typename T>
    class Array
    {
    public:
        // 类型定义
        using Type = T;
        using ConstType = const T;
        using PtrType = T*;
        using ConstPtrType = const T*;
        using RefType = T&;
        using ConstRefType = const T&;

    public:
        /*
            构造函数，传入容量和数据指针, 将指针指向的数据作为初始数据
        */
        Array(size_t size, ConstPtrType data)
        {
            if(size <= 0)
            {
                m_size = 0;
                m_capacity = 0;
                m_data = nullptr;
            }
            else
            {
                m_size = size;
                m_capacity = size;
                m_data = new Type[m_capacity];
                for(int i = 0; i < m_size; ++i)
                {
                    m_data[i] = data[i];
                }
            }
        }

        ~Array()
        {
            delete[] m_data;
            m_data = nullptr;
            m_size = 0;
            m_capacity = 0;
        }

    public:
        // 功能函数

        /*
            在末尾插入元素，如果当前容量不足，则扩展容量
            - 时间复杂度: O(1)
        */
        void push_back(ConstType element)
        {
            if(m_size >= m_capacity)
            {
                int newCapacity = (m_capacity == 0) ? 1 : m_capacity * 2;
                resize(newCapacity);
            }
            m_data[m_size] = element;
            ++m_size;
        }

        /*
            删除末尾元素，如果当前大小为0，则不进行任何操作
            - 时间复杂度: O(1)
        */
        void pop_back()
        {
            if(m_size > 0)
            {
                --m_size;
            }
        }
        
        /*
            输出当前数组的所有元素，元素间以 delimiter 分隔
            - 默认分隔符为空格
            - 时间复杂度: O(n)
        */
        void print(const char* delimiter = " ") const
        {
            for(int i = 0; i < m_size; ++i)
            {
                std::cout << m_data[i];
                if(i < m_size - 1)
                {
                    std::cout << delimiter;
                }
            }
            std::cout << std::endl;
        }

        /*
            返回当前数组的大小
        */
        size_t size() const
        {
            return m_size;
        }

        /*
            清空数组
            - 时间复杂度: O(n)
        */
        void clear()
        {
            while(m_size > 0)
            {
                pop_back();
            }
        }

    private:
        // 基本函数

        /*
            调整容量, 将当前容量调整为 newCapacity
            - 如果 newCapacity 小于当前大小，则会截断多余的元素
            - 如果 newCapacity 大于当前容量，则会分配更多的空间
        */
        void resize(int newCapacity)
        {
            if(newCapacity == m_capacity)
                return;

            if(newCapacity <= 0)
            {
                delete[] m_data;
                m_data = nullptr;
                m_size = 0;
                m_capacity = 0;
                return;
            }

            PtrType newData = new Type[newCapacity];
            int elementsToCopy = (newCapacity < m_size) ? newCapacity : m_size;
            for(int i = 0; i < elementsToCopy; ++i)
            {
                newData[i] = m_data[i];
            }

            delete[] m_data;
            m_data = newData;
            m_size = elementsToCopy;
            m_capacity = newCapacity;
        }

    public:
        // 运算符重载

        /*
            重载下标运算符，返回对应索引的元素引用
            - 未进行索引越界检查
        */
        RefType operator[](int index)
        {
            return m_data[index];
        }

    private:
        // 成员变量
        PtrType m_data;   // 数据指针
        size_t m_size;       // 当前大小
        size_t m_capacity;   // 容量
    };
} // namespace YY
```

## 2.2 静态存储实现顺序表

需要预先知道顺序表的最大容量 `N`

```cpp
const int N = 1e6 + 10;     // 这里以1e6为例，按需调整

// 静态数组表示顺序表
int a[N];
int n = 0;      // 当前顺序表大小

/*
    在顺序表末尾添加元素 x
    注意：
    - 这里顺序表的下标从 1 开始
    - 如果要从 0 开始，可以将 a[++n] 改为 a[n++]
*/
void push_back(int x)
{
    if(n < N) a[++n] = x;
}

/*
    在顺序表头部添加元素 x
    分析：
    - 在插入前，已有 n 个元素，下标为 [1, n]
    - 我们将这 n 个元素移动到 [2, n + 1] 的位置，然后将 x 放在 a[1] 处
*/
void push_front(int x)
{
    if(n < N)
    {
        // 将所有元素后移一位
        for(int i = n; i >= 1; --i)
        {
            a[i + 1] = a[i];
        }
        
        // 将 x 放在表头
        a[1] = x;
        ++n;
    }
}

/*
    在任意位置 pos 插入元素 x
    - 如果 pos 不合法，则不进行任何操作
*/
void insert(int pos, int x)
{
    if(n < N && pos >= 1 && pos <= n + 1)
    {
        // 将 pos 及其后面的元素后移一位
        for(int i = n; i >= pos; --i)
        {
            a[i + 1] = a[i];
        }

        // 插入 x
        a[pos] = x;
        ++n;
    }
}

/*
    删除顺序表末尾的元素
    - 如果顺序表为空，则不进行任何操作
*/
void pop_back()
{
    if(n > 0)
    {
        --n;
    }
}

/*
    删除顺序表头部的元素
    - 如果顺序表为空，则不进行任何操作
*/
void pop_front()
{
    if(n > 0)
    {
        // 将所有元素前移一位
        for(int i = 1; i < n; ++i)
        {
            a[i] = a[i + 1];
        }
        --n;
    }
}

/*
    删除任意位置 pos 的元素
    - 如果 pos 不合法，则不进行任何操作
*/
void erase(int pos)
{
    if(pos >= 1 && pos <= n)
    {
        // 将 pos 之后的元素前移一位
        for(int i = pos; i < n; ++i)
        {
            a[i] = a[i + 1];
        }
        --n;
    }
}

/*
    查找元素 x 在顺序表中的位置，返回下标
    - 如果不存在，则返回 -1
    - 时间复杂度: O(n)
*/
int find(int x)
{
    for(int i = 1; i <= n; ++i)
    {
        if(a[i] == x)
        {
            return i;
        }
    }
    return -1;
}

/*
    获取顺序表中指定位置 index 的元素
    - 如果 index 不合法，则返回一个错误值
*/
int at(int index)
{
    if(index >= 1 && index <= n)
    {
        return a[index];
    }
    return -0x3f3f3f3f; // 返回一个错误值
}

/*
    修改顺序表中指定位置 index 的元素为 x
    - 如果 index 不合法，则不进行任何操作
*/
void set(int index, int x)
{
    if(index >= 1 && index <= n)
    {
        a[index] = x;
    }
}

/*
    清空顺序表
    - 时间复杂度: O(1)
*/
void clear()
{
    n = 0;
}
```

# 3. `vector` 的使用

vector 是 C++ STL 中提供的顺序表容器，封装了动态数组的实现，提供了丰富的接口。

## 3.1 引入头文件

```cpp
#include <vector>
```

## 3.2 定义 vector

```cpp
std::vector<int> vec;               // 定义一个存储 int 类型的空 vector
std::vector<int> vec2(10);          // 定义一个存储 int 类型的 vector，初始大小为 10，默认值为 0
std::vector<int> vec3(10, 5);       // 定义一个存储 int 类型的 vector，初始大小为 10，默认值为 5
std::vector<int> vec4 = {1, 2, 3, 4, 5}; // 定义并初始化一个存储 int 类型的 vector
std::vector<std::string> strVec; // 定义一个存储 string 类型的空 vector
std::vector<std::vector<int>> vecOfVecs; // 定义一个存储 vector<int> 类型的 vector
```

## 3.3 常用操作

```cpp
vec.push_back(10);                  // 在末尾添加元素 10
vec.pop_back();                     // 删除末尾元素
int size = vec.size();              // 获取当前大小
bool isEmpty = vec.empty();         // 判断是否为空
vec.clear();                        // 清空 vector
int firstElement = vec.front();     // 获取第一个元素
int lastElement = vec.back();       // 获取最后一个元素
vec[0] = 20;                        // 通过下标访问和修改元素

for(int i = 0; i < vec.size(); ++i) // 遍历
{
    std::cout << vec[i] << " ";
}

for(const auto& element : vec)      // 范围基 for 循环遍历
{
    std::cout << element << " ";
}
```

注：
1. vector 会自动管理内存，动态调整容量，因此 `clear()` 操作的时间复杂度为 O(n)。
2. vector 支持迭代器，可以使用迭代器进行更灵活的遍历和操作。