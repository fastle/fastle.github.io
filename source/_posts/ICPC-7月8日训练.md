---
title: ICPC--7月8日训练
date: 2022-07-08 16:13:48
tags: ['ICPC', '日常']
---

# 刷题 Codeforces Round #804 (Div. 2)

## A The Third Three Number Problem
题目：给一个数n，构造a,b,c使得$(a^b) + (b ^ c) + (a ^ c) = n$

- 考虑三个二进制数两两的亦或的和不可能是奇数， 而对于偶数x 我们构造 $0, 0, \frac{x}{2}$ 即可

```cpp
#include<cstdio>
#include<iostream>
#include<algorithm>
#include<queue>
#include<cmath>
#define M 
using namespace std;

int read()
{
    int nm = 0, f = 1;
    char c = getchar();
    for(;!isdigit(c); c = getchar()) if(c == '-') f = -1;
    for(;isdigit(c); c = getchar()) nm = nm * 10 + c - '0';
    return nm * f;
}


int main()
{
    // freopen("test.in", "r", stdin); freopen("test.out", "w", stdout);
    int t = read();
    while(t--)
    {
        int n = read();
        if(n & 1)
        {
            cout << "-1\n";
        }
        else{
            cout << "0 0 " << (n / 2) << "\n";
        }
    }
    
    return 0;
}

```

## B Almost Ternary Matrix
题目：构造一个n*m的01矩阵，其中n,m均为偶数， 使得每个1四联通周围有两个0，使得每个0四联通周围有两个1

- 构造2 * 2 之后反转即可

```cpp
#include<cstdio>
#include<iostream>
#include<algorithm>
#include<queue>
#include<cmath>
#define M 1000
using namespace std;

int read()
{
    int nm = 0, f = 1;
    char c = getchar();
    for(;!isdigit(c); c = getchar()) if(c == '-') f = -1;
    for(;isdigit(c); c = getchar()) nm = nm * 10 + c - '0';
    return nm * f;
}
int note[101][101];

int main()
{
    // freopen("test.in", "r", stdin); freopen("test.out", "w", stdout);
    int t = read();
    while(t--)
    {
        int n = read(), m = read();
        for(int i = 1; i * 2 <= n; i++)
        {
            for(int j = 1; j * 2 <= m; j++)
            {
                int xn = (i - 1) * 2 + 1, yn = (j - 1) * 2 + 1;
                if((i + j) & 1) 
                {
                    note[xn][yn] = 1;
                    note[xn + 1][yn] = 0;
                    note[xn][yn + 1] = 0;
                    note[xn + 1][yn + 1] = 1;
                }
                else
                {
                    note[xn][yn] = 0;
                    note[xn + 1][yn] = 1;
                    note[xn][yn + 1] = 1;
                    note[xn + 1][yn + 1] = 0;
                }
            }
        }
        for(int i = 1; i <= n; i++)
        {
            for(int j = 1; j <= m; j++)
            {
                cout << note[i][j] << " ";
            }
            cout << "\n";
        }
    }
    return 0;
}
```


## C The Third Problem
题目：给定长度为n的排列，问与其每个子区间mex都相同的排列有多少个

- 考虑哪些位置是确定的，哪些位置是可变的，从小到大考虑，每个可变的数字都可以在当前固定的区间内随便找个空，每个空是等价的所以只需考虑剩下多少个空就行

```cpp
#include<cstdio>
#include<iostream>
#include<algorithm>
#include<queue>
#include<cmath>
#define M 100010
using namespace std;

int read()
{
    int nm = 0, f = 1;
    char c = getchar();
    for(;!isdigit(c); c = getchar()) if(c == '-') f = -1;
    for(;isdigit(c); c = getchar()) nm = nm * 10 + c - '0';
    return nm * f;
}
const int mod = 1000000007;
int note[M], pos[M];
int main()
{
    // freopen("test.in", "r", stdin); freopen("test.out", "w", stdout);
    int t = read();
    while(t--)
    {
        int n = read();
        for(int i = 1; i <= n; i++)
        {
            note[i] = read();
            pos[note[i]] = i;
        }
        int ans = 1;
        int l = pos[0], r = pos[0];
        for(int i = 1; i < n; i++)
        {
           int p = pos[i];
           if(p > r || p < l)
           {
               l = min(l, p);
               r = max(r, p);
           }
           else{
               ans = 1ll * ans * (r - l - i + 1)  % mod;
           }
        }
        cout << ans << "\n";
    }
    return 0;
}

```
## D Almost Triple Deletions

题目：长度为n的序列，元素值域为[1,n], 可以不断执行一个操作，将两个相邻的不同的数字删除，问最多能剩下多少个相同的数字

- dp来做，f[i]表示以第i个元素结尾的，剩下的元素都和第i个元素相同的最大情况
- 预处理出每个区间是否能够被完全删除
- 转移的时候要使得两个区间之间能够完全删除才能转移

```cpp
#include<bits/stdc++.h>
#define M 5050
using namespace std;

int read()
{
    int nm = 0, f = 1;
    char c = getchar();
    for(;!isdigit(c); c = getchar()) if(c == '-') f = -1;
    for(;isdigit(c); c = getchar()) nm = nm * 10 + c - '0';
    return nm * f;
}

int f[M][M], a[M], cnt[M];
int dp[M];

int main()
{
    // freopen("test.in", "r", stdin); freopen("test.out", "w", stdout);
    int t = read();
    memset(f, 1, sizeof(f));
    while(t--)
    {
        int n = read();
        for(int i = 1; i <= n; i ++) a[i] = read();
        for(int i = 1; i <= n; i++) 
        {
            for(int j = 0; j <= n; j++) cnt[j] = 0;
            int maxx = 0;
            for(int j = i; j <= n; j++)
            {
                cnt[a[j]]++;
                maxx = max(maxx, cnt[a[j]]);
                if(((j - i) & 1) && maxx * 2 <= (j - i + 1)) f[i][j] = 1;
                else f[i][j] = 0;
                // cout << f[i][j] << " ";
            }
            // cout << "\n";
        }
        int ans = 0;
        for(int i = 1; i <= n; i++)
        {
            if( f[1][i - 1]) dp[i] = 1;
            else dp[i] = 0;
            for(int j = 1; j < i; j++)
            {
                if(a[j] == a[i] && f[j + 1][i - 1] && dp[j] > 0) dp[i] = max(dp[i], dp[j] + 1);
            }
            // cout << dp[i] << " ";
            if(f[i + 1][n]) ans = max(ans, dp[i]);
        }
        cout << ans << "\n";
    }
    
    return 0;
}
```

## E 没看懂 暂缺