
需要预先学习：
- [[堆和priority_queue]]

题目网址：[第 k 小](https://ac.nowcoder.com/acm/problem/214362)

# 题目内容

![[img/4/6.png]]

# 代码

```cpp
#include <iostream>
#include <queue>


using namespace std;

const int N = 2e5 + 10;

int n, m, k;
priority_queue<int> q;      // 大根堆，我们维护其大小为k

// 确保最小的k个数在堆中，堆顶就是第 k 小的数
void insert(int x)
{
    if(q.size() < k) q.push(x);
    else if(x < q.top())
    {
        q.pop();
        q.push(x);
    }
}

void query()
{
    if(q.size() < k) cout << -1 << endl;        // 如果堆中元素不足k个，输出-1
    else cout << q.top() << endl;               // 否则输出堆顶元素，即第 k 小的数
}

int main()
{
    cin >> n >> m >> k;
    for(int i = 1, x; i <= n; i++)
    {
        cin >> x;
        insert(x);
    }

    while(m--)
    {
        int op, x;
        cin >> op;
        if(op == 1)
        {
            cin >> x;
            insert(x);
        }
        else query();
    }

    return 0;
}
```