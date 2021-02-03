## 张炳琪9.3作业
[toc]
#### 概述
- 今天写了AC自动机， 上午做了一个抽离fail树的题，可能是理解还不太深入，一直卡到下午三点
- 之后就好多了，字符串还是挺不好写的

##### 阿狸的打字机

```cpp
/*
首先fail指针指向的是当前串的最长后缀，那么由于匹配串也是给定的那么我们就可以就可以对于模板串的每一个节点暴力跳fail判断是否是匹配串的终止节点
这样统计答案可以N^2 ??? 反正是能得到50分

然后发现对于模板串相同的询问  我们可以进行一边操作更新很多答案来优化它、

然后我们发现fail指针构成一棵树， 那么我们将它反向，对于每个x子树中有多少可行的点
但是我们发现对于每次查找，y都要完全跑一边并且所有经过的点都要赋值成1， 虽然说这个可以根据dfs序映射到树状数组然后查询区间和吧， 但是只是把一个N换成了log， 依复杂度不对

然后我们发现可以优化y串的插入， 用dfs的方法枚举所有串， 当完整处理到某个串时就能够回答关于该串的询问了

另外值得一提的是字符串的处理， 首先如果可行就在trie树上走， 如果是P就记录位置，如果是B就跳到父亲
这样就不用记录字符串了

哪里写错了QWQ 
*/
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<iostream>
#include<queue>
#include<string>
#define M  201000
#define ll long long
using namespace std;
int read() {
	int nm = 0, f = 1;
	char c = getchar();
	for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
	for(; isdigit(c); c = getchar()) nm = nm * 10 + c - '0';
	return nm * f;
}
int ch[M][26], tch[M][26], fail[M], fa[M];
bool lef[M];
vector<int>to[M];
vector<int>to1[M];
vector<int>note[M];
int dfn[M], cnt, dft, tot;
int tr[M],ed[M];
int lowbit(int x) {
	return x & (-x);
}
char s[M];
int n, notex[M], ans[M];
int biao[M];
void getfail() {
	queue<int> q;
	q.push(0);
	while(!q.empty()) {
		int op = q.front();
		q.pop();
		for(int i = 0; i < 26; i++) {
			int x = ch[fail[op]][i];
			if(op == 0) x = 0;
			if(ch[op][i]) q.push(ch[op][i]), fail[ch[op][i]] = x;
			else ch[op][i] = ch[fail[op]][i];
		}
	}
	for(int i = 1; i <= cnt; i++) to1[fail[i]].push_back(i);
}

void dfs1(int now) {
	dfn[now] = ++dft;
	for(int i = 0; i < to1[now].size(); i++) {
		int vj = to1[now][i];
		dfs1(vj);
	}
	ed[now] = dft;
}


int insert(int pl, int v) {
	while(pl <= dft) {
		tr[pl] += v;
		pl += lowbit(pl);
	}
}

int sum(int x) {
	int ans = 0;
	while(x > 0) {
		ans += tr[x];
		x -= lowbit(x);
	}
	return ans;
}

int query(int x) {
	int l = dfn[x] - 1, r = ed[x];
	return sum(r) - sum(l);
}


void dfs(int now) {
	insert(dfn[now], 1);
	if(lef[now]) {
		for(int i = 0; i < note[now].size(); i++) {
			int y = note[now][i];
			for(int j = 0; j < to[y].size(); j++) {
				int id = to[y][j];
				int x = biao[notex[id]];
				ans[id] += query(x);
			}
		}
	}
	for(int i = 0; i < 26; i++) {
		if(tch[now][i])
			dfs(tch[now][i]);
	}
	insert(dfn[now], -1);
}

int main() {
	scanf("%s", s);
	int len = strlen(s);
	int now = 0;
	for(int i = 0; i < len; i++) {
		char c = s[i];
		if(c == 'B') now = fa[now];
		else if(c == 'P') tot++, note[now].push_back(tot), biao[tot] = now, lef[now] = true;
		else {
			int x = c - 'a';
			if(ch[now][x]) now = ch[now][x];
			else ch[now][x] = tch[now][x] = ++cnt,fa[cnt] = now,  now = ch[now][x];
		}
	}
	getfail();
	dfs1(0);
	n = read();
	for(int i = 1; i <= n; i++) {
		int x = read(), y = read();
		
		to[y].push_back(i);
		notex[i] = x;
	}
	dfs(0);
	for(int i = 1; i <= n; i++) cout << ans[i] << "\n";
	return 0;
}
/*
aPaaPaPaPaP
4
1 2
1 4
1 5
2 5

aPbPaPbPababP
5
3 2
2 3
5 5
1 5
3 5

aBaPaaBP
3
1 2
2 1
*/
```

##### [HNOI2010]合唱队

```cpp
/*
动态规划的区间问题
考虑倒推整个过程
每一步考虑和下一次加入的大小关系， 满足大小关系的才能加入答案
发现区间的规模总数 是N ^ 2	的而且每次转移时O1的 所以可以做到N ^2 的复杂度
 

*/


#include<cstdio>
#include<cstdlib>
#include<cstring>
using namespace std;
const int MusiXN=1010;
const int mod=19650827;
int N,usi[MusiXN],dp[MusiXN][MusiXN][2];
int zzzz(int l,int r,int op) {
	if(l==r) dp[l][r][op]=((usi[l]<usi[l+1]&&op)|(usi[l]>usi[l-1]&&!op))?1:0;
	if(dp[l][r][op]!=-1) return dp[l][r][op];
	int ans=0;
	if(op==1) {
		if(usi[l]<usi[r+1]) ans+=zzzz(l+1,r,0);
		if(usi[r]<usi[r+1]) ans+=zzzz(l,r-1,1);
	} else {
		if(usi[l]>usi[l-1]) ans+=zzzz(l+1,r,0);
		if(usi[r]>usi[l-1]) ans+=zzzz(l,r-1,1);
	}
	return dp[l][r][op]=ans%mod;
}
int main() {
	scanf("%d",&N);
	memset(dp,-1,sizeof(dp));
	for(int i=1; i<=N; i++) scanf("%d",&usi[i]);
	printf("%d\n",zzzz(1,N,0) + zzzz(1, N, 1));
	return 0;
}
```

##### 「BZOJ2754」 喵星球上的点名
```cpp
/*
这个题的最大问题是字符集的 大小太大，不能用数组直接来存，所以想办法用map把他存起来

我们要求的是M串在哪些串中是可行的， 这个显然可以用抽离fail树来做，但是不幸的是两个不同的串可能是
同一只猫的， 这让我们感到很麻烦

那么我们把M串建立AC自动机， 每次查询一个名字串的时候不停地跳fail 看能不能跳到x

考虑如果我们在名和姓之间加上一个连接字符，那么我们就可以把它看做一个串来匹配了

不过和上一个题有区别的是，并不是所有的串都是AC自动机的原串， 所以需要把所有的名字串也加入进去
太麻烦了 直接暴力吧

*/
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<iostream>
#include<queue>
#include<map>
#define M  200100
#define ll long long
using namespace std;
int read() {
	int nm = 0, f = 1;
	char c = getchar();
	for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
	for(; isdigit(c); c = getchar()) nm = nm * 10 + c - '0';
	return nm * f;
}
map<int, int>ch[M];
map<int, int>::iterator it;
int note[M], be[M], ed[M], cnt;
int fail[M];
int n, m;
vector<int> end[M];
void insert(int id) {
	int l = read(), now = 0;
	for(int i = 1; i <= l ; i++) {
		int x = read();
		it = ch[now].find(x);
		if(it != ch[now].end()) now = it->second;
		else {
			cnt++, ch[now][x] = cnt, now = cnt;
		}
		if(i == l) end[now].push_back(id);
	}
}

void getfail() {
	queue<int>q;
	for(it = ch[0].begin(); it != ch[0].end(); it++) {
		q.push(it->second);
	}
	while(!q.empty()) {
		int op = q.front();
		q.pop();
		for(it = ch[op].begin(); it != ch[op].end(); it++) {
			int t = it->second, k = fail[op];
			while(k && !ch[op].count(it->first)) k = fail[k];
			if(ch[k].count(it->first))k = ch[k][it->first];
			
		}
	}
}
void work(int id) {
}

int main() {
	n = read(), m = read();
	int now = 0;
	for(int i = 1; i <= n; i++) {
		be[i] = now + 1;
		int l = read();
		for(int j = 1; j <= l; j++) note[++now] = read();
		note[++now] = -1;
		l = read();
		for(int j = 1; j <= l; j++) note[++now] = read();
		ed[i] = now;
	}
	getfail();
	for(int i = 1; i <= m; i++) {
		insert(i);
	}
	for(int i = 1; i <= n; i++) work(i);

	return 0;
}

```

##### poj3691
```cpp
/*这道题用AC自动机+dp做与其他匹配多模式串的AC自动机最大的不同就是：一个结点j的KIND个孩子不会有空，就是说当从结点通过一个不能往下连接的字符时，孩子要通过j的fail结点来找到，或者回到根。



所以在Trie树中安全结点中遍历时，就能得到到某个结点的修改值了。

母串的前i个前缀在Trie树中遍历，到达某个安全结点j需要修改的值为dp[i][j]

dp[i][son]=min{dp[i][son],dp[i-1][j]+(str[i-1]!=char(j->son))} ;//son是j的孩子str是从0开始的

初始状态为:dp[0][0]=0，其他为无穷      //0是根结点*/

#include<iostream>
#include<algorithm>
#include<cstring>
using namespace std;

const int KIND=4;
const int MAX=1005;
const int INF=10000000;

struct TrieNode {
	int index;
	bool unsafe;
	TrieNode *fail;
	TrieNode *next[KIND];
};

TrieNode memory[MAX];
int allocp;
int verID[100];//verID['A']=0,verID['C']=1....
int n,dp[MAX][MAX];
TrieNode *q[MAX];
char word[25],str[MAX];

TrieNode *CreateTrieNode() {
	TrieNode *p=&memory[allocp];
	p->index=allocp;
	allocp++;
	p->fail=NULL;
	p->unsafe=false;
	memset(p->next,0,sizeof(p->next));
	return p;
}

void InsertTrieNode(TrieNode *pRoot,char s[]) {
	TrieNode *p=pRoot;
	int i=0;
	while(s[i]) {
		int k=verID[s[i]];
		if(p->next[k]==NULL)
			p->next[k]=CreateTrieNode();
		i++;
		p=p->next[k];
	}
	p->unsafe=true;
}

void getfail() {
	queue<int> q;
	q.push(0);
	while(!q.empty()) {
		int op = q.front();
		q.pop();
		for(int i = 0; i < 26; i++) {
			int x = ch[fail[op]][i];
			if(op == 0) x = 0;
			if(ch[op][i]) q.push(ch[op][i]), fail[ch[op][i]] = x;
			else ch[op][i] = ch[fail[op]][i];
		}
	}
	for(int i = 1; i <= cnt; i++) to1[fail[i]].push_back(i);
}


int DP() {
	int i,j,k,son;
	int len=strlen(str);
	TrieNode *p;
	for(i=0; i<=len; i++)
		for(j=0; j<allocp; j++)
			dp[i][j]=INF;
	dp[0][0]=0;
	for(i=1; i<=len; i++)
		for(j=0; j<allocp; j++)
			if(dp[i-1][j]<INF) { //dp[i-1][j]小于INF,曾更新过,则更新孩子dp
				p=&memory[j];
				for(k=0; k<KIND; k++)
					if(p->next[k]->unsafe==0) { //在安全节点上找
						son=p->next[k]->index; //孩子节点位置
						dp[i][son]=min(dp[i][son],dp[i-1][j]+(verID[str[i-1]]!=k));//DP转移
					}
			}

	int ans=INF;
	for(i=0; i<allocp; i++)
		if(memory[i].unsafe==0 && dp[len][i]<ans)//求安全节点中，最小的dp[len][]值
			ans=dp[len][i];
	if(ans==INF)
		return -1;
	return ans;
}

int main() {
	int test=1;
	TrieNode *pRoot;
	verID['A']=0;
	verID['C']=1;
	verID['G']=2;
	verID['T']=3;
	while(scanf("%d",&n),n) {
		allocp=0;
		pRoot=CreateTrieNode();
		for(int i=0; i<n; i++) {
			cin>>word;
			InsertTrieNode(pRoot,word);
		}
		Build_AC_Automation(pRoot);
		cin>>str;
		printf("Case %d: %d\n",test++,DP());
	}
	system("pause");
	return 0;
}

```

##### POJ 1204 
```cpp
/*

由于要记录每个单词单词在矩阵的第一个字母顺序和方向，这有些困难，因为在匹配的过程中初始没办法回溯求初始位置，硬要这样的话肯定也行，比较麻烦。

考虑将单词反转，这样就是最后匹配到的那个位置，这样就不要回溯。

建好字典树后改装成AC自动机，然后在矩阵中从12个方向去查询，如果遇到危险节点（某个单词在这里结束）就可以记录这个单词的位置和方向了。
*/
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<iostream>
#include<queue>
#define M
#define ll long long
using namespace std;
int read() {
	int nm = 0, f = 1;
	char c = getchar();
	for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
	for(; isdigit(c); c = getchar()) nm = nm * 10 + c - '0';
	return nm * f;
}
int n, m, w;
string s[MAXN];
int pos[MAXN][3];
int dir[8][2] = { 0,1,0,-1,1,0,-1,0,1,1,-1,-1,1,-1,-1,1 };
char ch[9] = "CGEADHFB";
int head, tail;

struct node {
	node *fail;
	node *next[26];
	int id;
	node() {
		fail = NULL;
		id= 0;
		rep(i, 0, 26) next[i] = NULL;
	}
}*q[1000010];
node *root;

void insert(string s, int id) {
	node *p = root;
	int len = s.length();
	rep(i, 0, len) {
		int temp = s[i] - 'A';
		if (p->next[temp] == NULL) p->next[temp] = new node();
		p = p->next[temp];
	}
	p->id = id;
}

void build_ac() {
	q[tail++] = root;
	while (head != tail) {
		node *p = q[head++];
		node *temp = NULL;
		rep(i, 0, 26) if (p->next[i] != NULL) {
			if (p == root) p->next[i]->fail = root;
			else {
				temp = p->fail;
				while (temp != NULL) {
					if (temp->next[i] != NULL) {
						p->next[i]->fail = temp->next[i];
						break;
					}
					temp = temp->fail;
				}
				if (temp == NULL) p->next[i]->fail = root;
			}
			q[tail++] = p->next[i];
		}
	}
}

void query(int x, int y, int d, int id) {
	node *p = root;
	while (x >= 0 && x < n && y >= 0 && y < m) {
		int index = s[x][y] - 'A';
		while (p->next[index] == NULL && p != root) p = p->fail;
		p = p->next[index];
		if (p == NULL) p = root;
		node *temp = p;
		while (temp != root && temp->id) {
			int k = temp->id;
			if (pos[k][0] > x || pos[k][0] == x && pos[k][1] > y)
				pos[k][0] = x, pos[k][1] = y, pos[k][2] = id;
			temp = temp->fail;
		}
		x += dir[d][0];
		y += dir[d][1];
	}
}

int main() {
	ios::sync_with_stdio(false), cin.tie(0);
	cin >> n >> m >> w;
	root = new node();
	rep(i, 0, n) cin >> s[i];
	rep(i, 1, w + 1) {
		string a;
		cin >> a;
		reverse(all(a));
		insert(a, i);
		pos[i][0] = pos[i][1] = INF;
	}
	build_ac();
	rep(i, 0, n) {
		query(i, 0, 0, 1), query(i, m - 1, 1, 0);
		query(i, 0, 7, 6), query(i, m - 1, 6, 7);
		query(i, 0, 4, 5), query(i, m - 1, 5, 4);
	}
	rep(i, 0, m) {
		query(0, i, 2, 3), query(n - 1, i, 3, 2);
		query(0, i, 6, 7), query(n - 1, i, 7, 6);
		query(0, i, 4, 5), query(n - 1, i, 5, 4);
	}
	rep(i, 1, w + 1)
	cout << pos[i][0] << ' ' << pos[i][1] << ' ' << ch[pos[i][2]] << endl;
	return 0;
}

```
