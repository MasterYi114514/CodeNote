
需要预先学习：
- [[二叉树]]

# 1. 堆的定义

堆（Heap）是一种特殊的树形数据结构，满足以下两个条件：
1. **完全二叉树**：堆必须是一棵完全二叉树。
2. **堆性质**：对于最大堆/大根堆（Max Heap），每个节点的值都必须大于或等于其子节点的值；对于最小堆/小根堆（Min Heap），每个节点的值都必须小于或等于其子节点的值。

# 2. 堆的实现

堆是完全二叉树，因此可以用一个数组来进行存储，即：
对于下标为 `i` 的节点（i的取值范围为 [1,n]）：
- 左子节点的下标为 `2*i`
- 右子节点的下标为 `2*i`
- 父节点的下标为 `i/2`

以下内容均按实现大根堆进行，对于小根堆的实现只需将比较条件反过来即可。

## 2.1 向上调整算法

`向上调整算法` 可以在插入新元素后保持堆的性质。具体步骤如下：
1. 将新元素插入到堆的末尾。
2. 将新元素与其父节点进行比较，如果不满足堆性质，则交换它们的位置。
3. 重复步骤2，直到堆性质得到满足或者到达根节点。

```cpp
const int N = 1e6 + 10;
int size;                      // 堆的大小
int heap[N];                // 堆数组

void up(int child)
{
    int parent = child / 2;

    while(parent >= 1 && heap[parent] < heap[child])
    {
        // 交换 parent 和 child 的值
        swap(heap[parent], heap[child]);

        // 更新 child 和 parent 的值，继续向上调整
        child = parent;
        parent = child / 2;
    }
}
```

算法分析：
- 时间复杂度：O(log n)，其中 n 是堆的大小。因为在最坏情况下，可能需要调整到根节点。
- 空间复杂度：O(1)，因为只使用了常数级别的额外空间。

## 2.2 向下调整算法

`向下调整算法` 可以在删除堆顶元素后保持堆的性质。具体步骤如下：
1. 将堆顶元素与堆的最后一个元素交换位置。
2. 删除堆的最后一个元素（即原来的堆顶元素）。
3. 将新的堆顶元素与其子节点进行比较，如果不满足堆性质，则交换它们的位置。
4. 重复步骤3，直到堆性质得到满足或者到达叶子节点。

```cpp
const int N = 1e6 + 10;
int size;                       // 堆的大小
int heap[N];                    // 堆数组

void down(int parent)
{
    int child = parent * 2;        // 左孩子结点下标

    while(child <= size)
    {
        // 找出最大的孩子
        int rchild = child + 1;    // 右孩子结点下标，注意此时可能没有右孩子，需要判断rchild <= size
        if(rchild <= size && heap[rchild] > heap[child])
        {
            child = rchild;
        }

        // 堆已经合法了
        if(heap[parent] >= heap[child])
        {
            break;
        }

        swap(heap[parent], heap[child]);
        parent = child;
        child = parent * 2;
    }
}
```

算法分析：
- 时间复杂度：O(log n)，其中 n 是堆的大小。因为在最坏情况下，可能需要调整到叶子节点。
- 空间复杂度：O(1)，因为只使用了常数级别的额外空间。

## 2.3 堆的具体实现

**创建一个堆**
```cpp
const int N = 1e6 + 10;
int size;                       // 堆的大小
int heap[N];                    // 堆数组
```

**插入元素**
```cpp
void insert(int x)
{
    heap[++size] = x;       // 将新元素插入到堆的末尾
    up(size);               // 向上调整以保持堆的性质
}
```

**删除堆顶元素**
```cpp
void pop()
{
    swap(heap[1], heap[size]);  // 将堆顶元素与最后一个元素交换位置
    size--;                     // 删除堆的最后一个元素
    down(1);                    // 向下调整以保持堆的性质
}
```

**获取堆顶元素**
```cpp
int top()
{
    return heap[1];             // 返回堆顶元素
}
```

**获取堆的大小**
```cpp
int size()
{
    return size;              // 返回堆的大小
}
```

# 3. priority_queue

## 3.1 优先级队列

普通的队列是先进先出（FIFO）的数据结构，而优先级队列（Priority Queue）是一种特殊的队列，其中每个元素都有一个优先级，元素在插入队列末尾后还会按照优先级顺序进行**处理**。优先级队列通常使用堆来实现。

上面的 `处理` 即调整堆的结构，使得优先级最高的元素位于堆顶，以便快速访问和删除，即我们上面的 `向上调整算法`。
同样地，当我们删除堆顶元素时，我们需要使用 `向下调整算法` 来保持堆的性质。

## 3.2 STL中的 `priority_queue`

在C++ STL中，`priority_queue` 是一个模板类，用于实现优先级队列。**默认情况下，`priority_queue` 实现的是最大堆**，即优先级最高的元素位于堆顶。

```cpp
#include <iostream>
#include <queue>

int main()
{
    std::priority_queue<int> pq;    // 创建一个优先级队列

    pq.push(10);        // 插入元素
    pq.push(20);
    pq.push(5);

    std::cout << "Top element: " << pq.top() << std::endl;              // 获取堆顶元素

    pq.pop();           // 删除堆顶元素

    std::cout << "Top element after pop: " << pq.top() << std::endl;    // 获取新的堆顶元素

    return 0;
}
```

在该例子中：
```
初始情况下，pq为空: []
插入10后，pq为: [10]
插入20后，pq为: [20, 10]（20是堆顶，因为它的优先级更高）
插入5后，pq为: [20, 10, 5]（20仍然是堆顶）
调用top()返回20，因为它是优先级最高的元素。
调用pop()后，pq为: [10, 5]（20被删除，10成为新的堆顶）
调用top()返回10，因为它现在是优先级最高的元素。
```
### 3.2.1 小根堆的实现

当需要实现最小堆时，可以使用 `std::greater` 作为比较函数：
```cpp
#include <queue>        // for std::priority_queue
#include <vector>       // for std::vector
#include <functional>   // for std::greater

// 创建一个最小堆   数据类型     底层容器           比较函数
std::priority_queue<int, std::vector<int>, std::greater<int>> minHeap;      // 创建一个最小堆
```

`std::greater` 是一个函数对象，可以认为它的实现如下:
```cpp
template <typename T>
struct greater
{
    // 当 a > b 时返回 true，否则返回 false
    // 运算符重载使得我们可以按照函数的方式调用结构体
    bool operator()(const T& a, const T& b) const
    {
        return a > b;       
    }
}
```

类似地，我们也可以实现一个自定义的比较函数来实现比较关系：
```cpp
bool compare(int a, int b)
{
    return a > b;   // 当 a > b 时返回 true，否则返回 false
}

std::priority_queue<int, std::vector<int>, decltype(&compare)> minHeap(compare);    // 创建一个最小堆，使用自定义比较函数
```

### 3.2.2 对于自定义类型的优先级队列

当需要在优先级队列中存储自定义类型时，需要提供一个比较函数来定义元素之间的优先级关系。例如，假设我们有一个 `Person` 结构体，我们希望按照年龄来排序：

```cpp
#include <queue>
#include <vector>

struct Person
{
    std::string name;
    int age;

    // 定义比较函数，按照年龄排序
    bool operator<(const Person& other) const
    {
        return age < other.age;   // 年龄较大的优先级更高
    }
};

std::priority_queue<Person> personQueue;    // 创建一个大根堆，存储 Person 对象
std::priority_queue<Person, std::vector<Person>, std::greater<Person>> minPersonQueue;    // 创建一个小根堆，存储 Person 对象
```