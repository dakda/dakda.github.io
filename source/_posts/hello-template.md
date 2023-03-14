---
title: 背包问题
---

# 01背包

状态表示：

1.集合：所有只考虑前i个物品，且体积不超过j的选项集合

2.属性：MAX

状态计算：

​         集合划分：    f(i,j)

所有选择第i个物品的方案          所有不选第i个物品的方案

```
f(i,j) = max(f(i - 1,j),f(i - 1,j - vi) + wi)
```

## 二维数组

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;
const int N = 1010;
int n,m;
int f[N][N];      //递推数组，第一个下标为：物品选择范围为0 - n，第二个下标为当前背包的最大承重，值为当前情况下背包物品的最大价值
int v[N],w[N];    //w存放物体容量，v存放物品价值
int main()
{
    cin >> n >> m;
    for (int i = 1;i <= n;i++)
        cin >> v[i] >> w[i];
    for (int i = 1;i <= n;i++){
        for (int j = 0;j <= m;j++){
            f[i][j] = f[i - 1][j];
            if (j >= v[i])
                f[i][j] = max(f[i][j],f[i - 1][j - v[i]] + w[i]);  
                //最关键的递推公式，f[i][j]的值从选第i个物品，和不选第i个物品这两种情况中选最大
                //假设不选第i个物品，则f[i][j]等于f[i - 1][j]
                //假设选第i个物品，我们的容积要为第i个物品留下空间
                //所以其值为:选择的第i个物品的价值加上符合以下情况：
                //(物品只能在0 - （i - 1）中挑选，且最大承重为j - w[i])的最大价值

        }
    }
    cout << f[n][m] << endl;
}

```



## 滚动数组优化（一维）



```c++
int main()
{
    cin >> n >> m;
    for (int i = 1;i <= n;i++)
        cin >> v[i] >> w[i];
    for (int i = 1;i <= n;i++){
        for (int j = m;j >= 0;j--){
            f[j] = f[j];
            //f[i][j] = f[i - 1][j];
            if (j >= v[i])
                f[j] = max(f[j],f[j - v[i]] + w[i]);  
               // f[i][j] = max(f[i][j],f[i - 1][j - v[i]] + w[i]); 

        }
    }
    cout << f[n][m] << endl;
}

```

## 为什么第二层循环倒序遍历

我们要将二维数组等价替换为一维数组。

我们要保证

```c++
if (j >= v[i])
                f[j] = max(f[j],f[j - v[i]] + w[i]);  
               // f[i][j] = max(f[i][j],f[i - 1][j - v[i]] + w[i]); 
若要保证f[j - v[i]]是上一层i - 1的f[j - v[i]]我们需要从大到小遍历
这样当我们遍历第i层的f[j]的时候，f[j - v[i]]还没被更新，一定是上一层的
```



## 数字组合（属性为数量的01背包问题）

> 给定N个正整数A1,A2,...,AN,从中选出n个数使他们和为m，总共几种方案。

```c++
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 10010;

int n, m;
int f[N];  //记录和为i的数量

int main()
{
    cin >> n >> m;
    f[0] = 1;     //一个不选即为0
    for (int i = 0; i < n; i ++ )
    {
        int v;
        cin >> v;
        //从前i个物品选，且总体积是j的物品选法集合
        //f(i,j) = f(i - 1,j) + f(i - 1,j - v);
        for (int j = m; j >= v; j -- ) f[j] += f[j - v];
    }

    cout << f[m] << endl;

    return 0;
}
```





# 完全背包问题

跟01背包不同就是，我物品可以无限选



## 二维数组

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;
const int N = 1010;
int n,m;
int f[N][N];      //递推数组，第一个下标为：物品选择范围为0 - n，第二个下标为当前背包的最大承重，值为当前情况下背包物品的最大价值
int v[N],w[N];    //w存放物体容量，v存放物品价值
int main()
{
    cin >> n >> m;
    for (int i = 1;i <= n;i++)
        cin >> v[i] >> w[i];
    for (int i = 1;i <= n;i++){
        for (int j = 0;j <= m;j++){
            f[i][j] = f[i - 1][j];
            if (j >= v[i])
                f[i][j] = max(f[i][j],f[i][j - v[i]] + w[i]);  
                //跟0,1背包最大不同就是，物品可以重复选，
                //所以我在决定选i物品之后，我剩下容积的最大值仍可考虑选取i物品

        }
    }
    cout << f[n][m] << endl;

}
```



## 滚动数组优化

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int f[N];

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= n; i ++ ) cin >> v[i] >> w[i];

    for (int i = 1; i <= n; i ++ )
        for (int j = v[i]; j <= m; j ++ )
            f[j] = max(f[j], f[j - v[i]] + w[i]);    //两者等价，并且能将判断条件优化进循环
    /*if (j >= v[i])
                f[i][j] = max(f[i][j],f[i][j - v[i]] + w[i]); */
    
    cout << f[m] << endl;
    
    return 0;

}


```



## 买书.acwing1023（属性为数量的完全背包问题）

```
#include <iostream>

using namespace std;

const int N = 5, M = 1010;

int n = 4, m;
int v[N] = {0, 10, 20, 50, 100};
int f[N][M];

int main()
{
    //input
    cin >> m;

    //dp
    for (int i = 0; i <= 4; i ++ )
        f[i][0] = 1;
    for (int i = 1; i <= n; ++ i)
    {
        for (int j = 1; j <= m; ++ j)
        {
            for (int k = 0; v[i] * k <= j; ++ k)
            {
                f[i][j] += f[i - 1][j - v[i] * k];
            }
        }
    }

    //output
    cout << f[n][m] << endl;

    return 0;
}

```





# 多重背包问题

每个物品限量s[i],加一重循环遍历物品数量

``` c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 110;

int n, m;
int v[N], w[N], s[N];
int f[N][N];

int main()
{
    cin >> n >> m;

    for (int i = 1; i <= n; i ++ ) cin >> v[i] >> w[i] >> s[i];
    
    for (int i = 1; i <= n; i ++ )
        for (int j = 0; j <= m; j ++ )
            for (int k = 0; k <= s[i] && k * v[i] <= j; k ++ )
                f[i][j] = max(f[i][j], f[i - 1][j - v[i] * k] + w[i] * k);   //加一重循环遍历物品数量
    
    cout << f[n][m] << endl;
    return 0;

}


```



## 





# 分组背包问题

> 有 N 种物品和一个容量是 V 的背包。
>
> 第 i 种物品最多有 si件，每件体积是 vi，价值是 wi。
>
> 求解将哪些物品装入背包，可使物品体积总和不超过背包容量，且价值总和最大。
> 输出最大价值。

状态表示：

1.集合：所有只考虑前i组物品，且体积不超过j的选项集合

2.属性：MAX

状态计算：

​         集合划分：    f(i,j)

所有不选择第i组物品的方案          所有选择第i组物品中第1个物品的方案, .... 所有选择第i组物品中第k个物品的方案

```c++
  f[j] = max(f[j], f[j - v[i][k]] + w[i][k]);
```

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 110;

int n, m;
int v[N][N], w[N][N], s[N];
int f[N];

int main()
{
    cin >> n >> m;

    for (int i = 1; i <= n; i ++ )
    {
        cin >> s[i];
        for (int j = 0; j < s[i]; j ++ )
            cin >> v[i][j] >> w[i][j];
    }

    for (int i = 1; i <= n; i ++ )
        for (int j = m; j >= 0; j -- )
            for (int k = 0; k < s[i]; k ++ )
                if (v[i][k] <= j)
                    f[j] = max(f[j], f[j - v[i][k]] + w[i][k]);

    cout << f[m] << endl;

    return 0;
}

```





# 二维费用背包问题

## 宠物小精灵之收服



