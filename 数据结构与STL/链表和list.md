
需要预先学习：
- [[顺序表和vector]]

# 1. 链表的基础概念

## 1.1 链表的定义

链表：用链式存储结构实现的线性表。

**链式存储结构**：每个元素包含数据域和指针域，指针域存储下一个元素的地址(也可能包含前一个元素的地址)。

## 1.2 链表的分类

按照指针域的不同，链表可以分为单链表、双链表和循环链表。
按照数据域的不同，链表可以分为单链表和多重链表。
同时，链表还可以分为带头结点和不带头结点两种。

# 2. 链表的静态模拟实现

## 2.1 单链表的静态模拟实现

以下模仿实现一个单链表的基本操作，该链表不带头结点，数据域为整型。

我们一般选取不可能作为有效下标的数值作为无效下标，例如 -1。

**链表的创建**

```cpp
const int N = 1e6 + 10;     // 这里以1e6为例，按需调整

int e[N];           // 存储节点的值
int ne[N];          // 存储节点的 next 指针
int head = -1;      // 链表头节点的下标，初始值为 -1，表示链表为空
int idx = 0;        // 下一个可用节点的下标
```

**在链表头插入元素**

```cpp
void push_front(int x)
{
    idx++;

    e[idx] = x;         // 1. 先给新节点赋值
    ne[idx] = head;     // 2. 新节点的 next 指针指向原来的头节点
    head = idx;        // 3. 更新头节点为新节点
}
```

**遍历链表**

可以参考[[循环常用细节#^42ec19]]

```cpp
void print()
{
    for(int i = head; i != -1; i = ne[i])
    {
        printf("%d ", e[i]);
    }

    printf("\n");
}
```

**按值查找元素**

方法一：遍历整个链表，直至找到值为 x 的节点，返回其下标；否则返回 -1。
- 时间复杂度：O(n)
- 空间复杂度：O(1)

```cpp
int find(int x)
{
    for(int i = head; i != -1; i = ne[i])
    {
        if(e[i] == x) return i;   // 找到值为 x 的节点，返回其下标
    }

    return -1;  // 未找到，返回 -1
} 
```

方法二：用一个数组 `mp` 记录每个值对应的节点下标，插入时更新 `mp`，查找时直接返回 `mp[x]`。
- 时间复杂度：O(1)
- 空间复杂度：O(n)
- 适用范围：
    1. 元素值在一个较小的范围内（如 0 到 1e6）
    2. 元素值不会重复

```cpp
int mp[N];
void init()
{
    for(int i = 0; i < N; i++) mp[i] = -1; // 初始化 mp 数组
}

// 修改 push_front 函数
void push_front(int x)
{
    idx++;
    e[idx] = x;         // 1. 先给新节点赋值
    ne[idx] = head;     // 2. 新节点的 next 指针指向原来的头节点
    head = idx;         // 3. 更新头节点为新节点
    mp[x] = idx;        // 4. 更新 mp 数组
}

int find(int x)
{
    return mp[x];  // 直接返回 mp[x]，未找到时返回 -1
} 
```

**在链表位置为`k`的地方插入元素**

```cpp
void insert(int k, int x)
{
    idx++;
    e[idx] = x, mp[x] = idx;            // 1. 先给新节点赋值, 并更新 mp 数组
    if(k == 0)
    {
        ne[idx] = head;     // 2. 新节点的 next 指针指向原来的头节点
        head = idx;         // 3. 更新头节点为新节点
    }
    else
    {
        ne[idx] = ne[k];    // 2. 新节点的 next 指针指向位置为 k 的节点的下一个节点
        ne[k] = idx;        // 3. 位置为 k 的节点的 next 指针指向新节点
    }
} 
```

**删除在链表位置为`k`的节点**

```cpp
void remove_at(int k)
{
    if(k == 0)
    {
        head = ne[head];    // 1. 更新头节点为原头节点
    }
    else
    {
        ne[k] = ne[ne[k]];  // 1. 位置为 k 的节点的 next 指针指向其下一个节点的下一个节点
    }
}
```

**删除值为`x`的节点**

```cpp
void erase(int x)
{
    int k = head;

    if(e[k] == x)
    {
        head = ne[head];    // 1. 更新头节点为原头节点
        return;
    }

    // 进行遍历查找，直到找到值为 x 的节点
    while(ne[k] != -1 && e[ne[k]] != x)
    {
        k = ne[k];
    }

    if(ne[k] != -1)
    {
        ne[k] = ne[ne[k]];  // 2. 位置为 k 的节点的 next 指针指向其下一个节点的下一个节点
    }
}
```

## 2.2 双向链表的静态模拟实现

这里实现带有头结点的双向链表。

当链表带有头结点时，我们的无效下标可以设置为头结点的下标，例如 0。

**链表的创建**

```cpp
const int N = 1e6 + 10;     // 这里以1e6为例，按需调整
int e[N];           // 存储节点的值
int prev[N];          // 存储节点的前驱指针
int next[N];          // 存储节点的后继指针
int idx = 1;        // 下一个可用节点的下标，0 作为头结点使用
int head = 0;      // 头结点下标
```

**在链表头部插入元素**

```cpp
void push_front(int x)
{
    idx++;
    e[idx] = x;                 // 1. 先给新节点赋值
    
    next[idx] = next[head];     // 2. 新节点的 next 指针指向原来头结点的下一个节点
    prev[idx] = head;           // 3. 新节点的 prev 指针指向头结点
    
    prev[next[head]] = idx;     // 4. 原来头结点的下一个节点的 prev 指针指向新节点
    next[head] = idx;           // 5. 头结点的 next 指针指向新节点
}
```

**遍历链表**

与单链表类似，不过这里从头结点的下一个节点开始遍历。

```cpp
void print()
{
    for(int i = next[head]; i != 0; i = next[i])
    {
        printf("%d ", e[i]);
    }

    printf("\n");
}
```

**按值查找元素**

与单链表类似。

```cpp
int find(int x)
{
    for(int i = next[head]; i != 0; i = next[i])
    {
        if(e[i] == x) return i;   // 找到值为 x 的
    }
    return -1;  // 未找到，返回 -1
}
```

同样也可以使用哈希表进行优化，但是这里的 `mp` 数组无需初始化。

```cpp
int mp[N];

// 修改 push_front 函数
void push_front(int x)
{
    idx++;
    e[idx] = x;                 // 1. 先给新节点赋值
    next[idx] = next[head];     // 2. 新节点的 next 指针指向原来头结点的下一个节点
    prev[idx] = head;           // 3. 新节点的 prev 指针指
    prev[next[head]] = idx;     // 4. 原来头结点的下一个节点的 prev 指针指向新节点
    next[head] = idx;           // 5. 头结点的 next
    mp[x] = idx;                // 6. 更新 mp 数组
}

int find(int x)
{
    return mp[x];  // 直接返回 mp[x]，未找到时返回 -1
} 
```

**在链表位置为`k`的地方插入元素**

```cpp
void insert(int k, int x)
{
    idx++;
    e[idx] = x;                 // 1. 先给新节点赋值

    next[idx] = next[k];       // 2. 新节点的 next 指针指向位置为 k 的节点的下一个节点
    prev[idx] = k;             // 3. 新节点的 prev 指针指向位置为 k 的节点

    prev[next[k]] = idx;       // 4. 位置为 k 的节点的下一个节点的 prev 指针指向新节点
    next[k] = idx;             // 5. 位置为 k 的节点的 next 指针指向新节点
}
```

**删除在链表位置为`k`的节点**

```cpp
void remove_at(int k)
{
    next[prev[k]] = next[k];   // 1. 位置为 k 的节点的前驱节点的 next 指针指向位置为 k 的节点的下一个节点
    prev[next[k]] = prev[k];   // 2. 位置为 k 的节点的下一个节点的 prev 指针指向位置为 k 的节点的前驱节点
}
```

# 3. STL中的链表: `list`

C++ STL 中提供了链表容器 `list`，封装了链表的实现，提供了丰富的接口。

## 3.1 定义 `list`

```cpp
#include <list>
std::list<int> l; // 定义一个存储整型的链表
```

## 3.2 常用操作

```cpp
l.push_back(10);          // 在链表末尾添加元素 10
l.push_front(20);         // 在链表头部添加元素 20
l.pop_back();             // 删除链表末尾的元素
l.pop_front();            // 删除链表头部的元素
l.insert(it, 30);        // 在迭代器 it 指向的位置前插入元素 30
l.erase(it);             // 删除迭代器 it 指向的元素
l.clear();                // 清空链表
l.size();                 // 返回链表中元素的个数
l.empty();                // 判断链表是否为空，返回 bool 值
l.front();                // 返回链表头部的元素
l.back();                 // 返回链表末尾的元素
```