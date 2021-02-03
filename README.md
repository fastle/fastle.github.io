- 考虑转换成序列上的问题， 之后保证每块大小有序， 之后修改暴力修改， 不是整块的归并重构
- 查询的时候二分答案， 然后大块二分找答案， 小块事先合并再二分答案就好
- 这样假设块大小为k， 那么修改复杂度`$O(\frac{n}{k} + k)$`， 查询复杂度`$O((\frac{n}{k} + 1)log^2n)$`
- 解得当`$k = \sqrt(n)logn$`时， 复杂度为`$O(n\sqrt(n)logn)$`
- 
- 
```cpp
/*
claris的神奇分块写法， 将二进制高位相同的一部分看作一块， 非常的好用



*/


#include<cstdio>

#include<algorithm>
#include<cstring>
#include<queue>
#include<iostream>
#define ll long long
#define mmp make_pair
#define M 100010
#define pair<int, int> p;
using namespace std;
int read() {
	int nm = 0, f = 1;
	char c = getchar();
	for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
	for(; isdigit(c); c = getchar()) nm = nm * 10 + c - '0';
	return nm * f;
}
vector<p> to[M];
p a[M << 1];
int n, m, lim, sta[M], ed[M], stq[M], edq[M], tag[M], block, k = 12, dft;

void dfs(int now, int f) {
	stp[now] = ++dft;
	a[dft] = mmp(f, dft);
	for(int i = 0; i < to[now].size(); i++) {
		int vj = to[now][i].first;
		dfs(vj, f + to[now][i].second);
	}
	enq[now] = dft;
}

void change(int o, int l, int r, int p) {
	static P a[M], b[M];
	int ca = 0, cb = 0, k = st[o], i, j,;
	for(int i = st[p]; i <= ed[o]; i++) if(a[i].second < l || a[i].second > r) A[++ca] = a[i];
		else B[++cb] = a[i], B[cb].first += p;
	i = j = 1;
	while(i <= ca && j <= cb) a[k++] = A[i] < B[j] ? A[i++] : B[j++];
	while(i <= ca) a[k++] = A[i++];
	while(j <= cb) a[k++] = B[j++];
}

void modify(int x, int y, int p) {
	int X = x >> k, Y = y >> k;
	for(int i = X + 1; i < Y; i++) tag[i] += p;
	change(X, x, Y, p);
	if(X < Y) change(Y, x, y, p);
}

int ask(int o, int p) {
	int l = st[o], r = ed[o], mid, t = l - 1;
	if(l > r) return 0;
	p -= tag[o];
	while(l <= r) if(a[mid = (l + r) >> 1].first <= p) l = (t = mid) + 1;
		else r = mid - 1;
	return t - st[o] + 1;
}

int kth(int x, int y, int K)
{
	if(K > y - x + 1) return -1;
	int X = x >> k, Y = y >> k, i, s = n + 1, e = n;

int main() {
	n = read(), m = read();
	read();
	for(int i = 2; i <= n; i++) {
		int x = read(), y = read();
		to[x].push_back(mmp(i, y));
		lim += y;
	}
	dfs(1, 0);
	block = n >> k;
	for(int i = 1; i <= n; i++) ed[i >> k] = i;
	for(int i = n; i; i--) st[i >> k] = i;
	for(int i = 0; i <= block; i++) sort(a + st[i], a + ed[i] + 1);
	while(m--) {
		int op = read(), x = read(), y = read();
		if(op == 1) cout << kth(stq[x], enq[x], y) << "\n";
		else modify(stq[x], enq[x], y), lim += y;
	}
	return 0;
}

```
