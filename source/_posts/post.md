
---
title: 楼兰图腾
---
先理解这道题是如何应用树状数组思想的。

树状数组只有两个操作，修改第i个数的值，查询前i个数的和。我们这道题里树状数组tr[N]记录的是每个数a[i]出现的次数。

我们第一遍循环for求的是前i个数中比i大的数的数量和比i小的数的数量。

```c++
 for(int i = 1; i <= n; i++)
    {
        int y = a[i]; //第i个数

        //在前面已加入树状数组的所有数中统计在区间[1, y - 1]的数字的出现次数
        Lower[i] = ask(y - 1); 

        //在前面已加入树状数组的所有数中统计在区间[y + 1, n]的数字的出现次数
        Greater[i] = ask(n) - ask(y);

        //将y加入树状数组，即数字y出现1次
        add(y, 1);
    }
```

这里add(y,1)是将y这个值出现的次数加1，ask(y-1)求的是数组元素落到[0,a[i] - 1]中的次数，即求的是左边比a[i]小的，所以greater[i]求的是左边比a[i]大的次数.





tr[x] = (x - lowbit(x),x);

![image-20230313080129172](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20230313080129172.png)

```c++
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

typedef long long LL;

const int N = 200010;

int n;
int a[N];
int tr[N];
int Greater[N], lower[N];

int lowbit(int x)
{
    return x & -x;
}

void add(int x, int c)
{
    for (int i = x; i <= n; i += lowbit(i)) tr[i] += c;
}

int sum(int x)
{
    int res = 0;
    for (int i = x; i; i -= lowbit(i)) res += tr[i];
    return res;
}

int main()
{
    scanf("%d", &n);

    for (int i = 1; i <= n; i ++ ) scanf("%d", &a[i]);

    for (int i = 1; i <= n; i ++ )
    {
        int y = a[i];
        Greater[i] = sum(n) - sum(y);
        lower[i] = sum(y - 1);
        add(y, 1);
    }

    memset(tr, 0, sizeof tr);
    LL res1 = 0, res2 = 0;
    for (int i = n; i; i -- )
    {
        int y = a[i];
        res1 += Greater[i] * (LL)(sum(n) - sum(y));
        res2 += lower[i] * (LL)(sum(y - 1));
        add(y, 1);
    }

    printf("%lld %lld\n", res1, res2);

    return 0;
}
```



# **add的原理实现**

为什么呢？很简单，要找到 i 的第一个祖先，我们只需要从 i 一直往右走，找到第一个层数比 i 高的结点即可。由于所有的[树结点](https://www.zhihu.com/search?q=树结点&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A785844116})都是向右整合的，这样找到的结点的信息区间一定覆盖 i 结点的信息区间。而既然 lowbit(i) 决定了位置 i 的结点高度，那么我们要找的其实就是第一个使得 lowbit(j) > lowbit(i)的结点 j。如何得到这样一个 j？那就直接以消去 i 的 lowbit 为目标，**直接加上一个能够使得 i 的 lowbit 增大的最小的数字**，很显然，这个数字也就是 lowbit(i)。这样一来，i+lowbit(i) 就是我们要找的下一个结点，如此往复向上寻找直到到达[根结点](https://www.zhihu.com/search?q=根结点&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A785844116})即可。



# sum的原理实现

图中每一个结点 i 上的红色箭头指向它的 i - lowbit(i) 的结果。可以看到，一个结点 i 的位置减去 lowbit(i) 之后，其实就代表着向左离开它所表示的信息区间。比如说如果 i=7，那么由之前所说的我们知道 i 的信息区间长度为 lowbit(7)=1， 那么我们记录下 [7, 7] 区间的信息，开始往回走。往回走就是减去当前信息区间的长度，到达刚才没有覆盖的区间，也就刚好是减去 lowbit(7)=1得到 i=6；然后 i 的信息区间长度为 lowbit(6)=2， 那么我们用 [5, 6] 区间的信息更新手上的信息，继续往回走，也就是减去 lowbit(6)=2得到 i=4；然后 i=4，区间长度为lowbit(4)=4，再用 [1, 4] 的信息来更新手上的信息，继续往前走，减去 lowbit(4)=4得到 i=0；此时说明我们已经考虑到了整个的[查询区间](https://www.zhihu.com/search?q=查询区间&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A785844116})，不需要继续了。



![img](https://pic1.zhimg.com/v2-b3f7bccdbbe043154137e123b7e2bfbb_r.jpg?source=1940ef5c)

