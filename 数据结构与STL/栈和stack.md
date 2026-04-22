
需要预先学习：
- [[顺序表和vector]]

# 1. 栈的相关概念

## 1.1 栈的定义

栈（Stack）是一种线性表，遵循后进先出（LIFO）的原则。栈中的元素只能在一端进行插入和删除操作，这一端被称为栈顶（Top），另一端被称为栈底（Bottom）。

**后进先出**：最后插入的元素最先被删除，即插入和删除操作都发生在栈顶。

## 1.2 栈的基本操作

栈的基本操作包括：
- **Push**：将一个元素压入栈顶。
- **Pop**：将栈顶的元素弹出。
- **Top**：获取栈顶元素但不删除它。
- **IsEmpty**：检查栈是否为空。
- **Size**：获取栈中元素的数量。
- **Clear**：清空栈中的所有元素。
- **Destroy**：销毁栈，释放相关资源。

# 2. 栈的实现

## 2.1 栈的静态模拟实现

可以用固定大小的数组来实现栈。需要维护一个指针（或索引）来表示当前栈顶的位置。

**创建栈**：初始化栈顶索引为0，表示栈为空。
```cpp
const int N = 1e6 + 10;

int stack[N], n;        // 栈的数组和当前栈顶索引
```

**Push操作**：将元素压入栈顶，并更新栈顶索引。
```cpp
void push(int x) {
    stack[++n] = x;     // 将元素x压入栈顶
}
```
注：`stack[++n]` 会使我们的有效下标从1开始，`n`表示当前栈中元素的数量。

**Pop操作**：将栈顶元素弹出，并更新栈顶索引。
```cpp
void pop() {
    if (n > 0) {
        n--;            // 弹出栈顶元素，更新栈顶索引
    }
}
```

**Top操作**：获取栈顶元素但不删除它。
```cpp
int top() {
    if (n > 0) {
        return stack[n]; // 返回栈顶元素
    }
    return -1;         // 栈为空时返回-1（或其他无效值）
}
```

**IsEmpty操作**：检查栈是否为空。
```cpp
bool isEmpty() {
    return n == 0;     // 当n为0时，栈为空
}
```

**Size操作**：获取栈中元素的数量。
```cpp
int size() {
    return n;          // 返回当前栈中元素的数量
}
```

**Clear操作**：清空栈中的所有元素。
```cpp
void clear() {
    n = 0;             // 将栈顶索引重置为0，表示栈为空
}
```

## 2.2 栈的动态模拟实现


# 3. `stack` 的使用

## 3.1 引入头文件
```cpp
#include <stack>
```

## 3.2 定义 stack
```cpp
std::stack<int> s; // 定义一个存储 int 类型的 stack
std::stack<std::string> strStack; // 定义一个存储 string 类型的 stack
```

## 3.3 常用操作
```cpp
s.push(10);                  // 将元素 10 压入栈顶
s.pop();                     // 弹出栈顶元素
int topElement = s.top();     // 获取栈顶元素但不删除它
bool isEmpty = s.empty();     // 判断栈是否为空
int size = s.size();          // 获取栈中元素的数量
```

## 3.4 使用事项

`STL` 中的 `stack` 基于 `deque` 实现，常数较大，效率低，可以用 `vector` 模拟实现 `stack` ，不难发现有如下操作对应关系：
```
vector        stack
push_back --- push
pop_back  --- pop
size      --- size
empty     --- empty
back      --- top
```