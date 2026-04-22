
需要预先学习：
- [[顺序表和vector]]

# 1. 队列的概念

## 1.1 队列的定义

队列（Queue）是一种先进先出（FIFO, First In First Out）的数据结构。
**先进先出**：最先插入的元素最先被删除，即插入操作发生在队尾（Rear），删除操作发生在队头（Front）。

## 1.2 队列的基本操作

队列的基本操作包括：
- **Enqueue**：将一个元素加入队尾。
- **Dequeue**：将队头的元素移除。
- **Front**：获取队头元素但不删除它。
- **IsEmpty**：检查队列是否为空。
- **Size**：获取队列中元素的数量。
- **Clear**：清空队列中的所有元素。
- **Destroy**：销毁队列，释放相关资源。

# 2. 队列的实现

## 2.1 队列的静态模拟实现

可以用固定大小的数组来实现队列。需要维护两个指针（或索引）来表示当前队头和队尾的位置。

**创建队列**：初始化队头索引为0，队尾索引为-1，我们用 `rear < front` 表示队列为空。
```cpp
const int N = 1e6 + 10;
int queue[N], front, rear; // 队列的数组和队头、队尾索引
```

**Enqueue操作**：将元素加入队尾，并更新队尾索引。
```cpp
void enqueue(int x)
{
    queue[++rear] = x; // 将元素x加入队尾
}
```

**Dequeue操作**：将队头元素移除，并更新队头索引。
```cpp
void dequeue()
{
    if (front <= rear) {
        front++;        // 移除队头元素，更新队头索引
    }
}
```

**Front操作**：获取队头元素但不删除它。
```cpp
int front()
{
    if (front <= rear) {
        return queue[front]; // 返回队头元素
    }
    return -1;             // 队列为空时返回-1（或其他无效值）
}
```

**IsEmpty操作**：检查队列是否为空。
```cpp
bool isEmpty()
{
    return front > rear;   // 当front大于rear时，队列为空
}
```

**Size操作**：获取队列中元素的数量。
```cpp
int size()
{
    return rear - front + 1; // 队列中元素的数量
}
```

**Clear操作**：清空队列中的所有元素。
```cpp
void clear()
{
    front = 0; rear = -1; // 重置front和rear，表示
    // 队列为空
}
```

## 2.2 队列的动态循环实现


# 3. `queue` 的使用

## 3.1 引入头文件
```cpp
#include <queue>
```

## 3.2 定义 queue

```cpp
std::queue<int> q; // 定义一个存储 int 类型的空 queue
std::queue<std::string> strQueue; // 定义一个存储 string 类型的空 queue
```

## 3.3 常用操作

```cpp
q.push(10);                     // 在队尾添加元素 10
q.pop();                        // 删除队头元素
int size = q.size();            // 获取当前大小
bool isEmpty = q.empty();       // 判断是否为空
int frontElement = q.front();   // 获取队头元素但不删除它
int backElement = q.back();     // 获取队尾元素但不删除它
q.clear();                      // 清空 queue（注意：std::queue 没有 clear 方法
```

# 4. 双端队列 与 `deque`

双端队列（Deque, Double-Ended Queue）是一种允许在两端进行插入和删除操作的数据结构。与普通队列不同，双端队列既可以在队头进行插入和删除，也可以在队尾进行插入和删除。

## 4.1 引入头文件
```cpp
#include <deque>
```

## 4.2 定义 deque

```cpp
std::deque<int> d; // 定义一个存储 int 类型的空 deque
std::deque<std::string> strDeque; // 定义一个存储 string 类型的空 deque
```

## 4.3 常用操作

```cpp
d.push_back(10);              // 在队尾添加元素 10
d.push_front(20);             // 在队头添加元素 20
d.pop_back();                 // 删除队尾元素
d.pop_front();                // 删除队头元素
int size = d.size();          // 获取当前大小
bool isEmpty = d.empty();     // 判断是否为空
int frontElement = d.front(); // 获取队头元素但不删除它
int backElement = d.back();   // 获取队尾元素但不删除它
d.clear();                    // 清空 deque
```

