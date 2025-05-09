```cpp
#if 1
#define MAXN 10003
#include <vector>
using namespace std;
int N, M;
struct Info {
	int r, c, len;
}info[MAXN];
vector<int> m[13][13], g[MAXN];
int gCnt, p[MAXN], r[MAXN], MAXG;
void init(int N, int M){
	::N = N, ::M = M, gCnt = 0;
	MAXG = N / M;
	for (int i = 0; i < 13; i++) for (int j = 0; j < 13; j++)
		m[i][j].clear();
}

bool overlap(int a, int b) {
	int a_sr = info[a].r, a_er = a_sr + info[a].len;
	int b_sr = info[b].r, b_er = b_sr + info[b].len;
	int a_sc = info[a].c, a_ec = a_sc + info[a].len;
	int b_sc = info[b].c, b_ec = b_sc + info[b].len;
	return !(a_er < b_sr&& b_er < a_sr&& a_ec < b_sc&& b_ec < a_sc);
}

int find(int x) {
	if (p[x] != x) return p[x] = find(p[x]);
	return p[x];
}

void unionSet(int x, int y) {
	x = find(x), y = find(y);
	if (x == y) return;
	if (r[x] < r[y]) swap(x, y);
	p[y] = x;
	if (r[x] == r[y]) r[x]++;
	for (int nx : g[y]) g[x].push_back(nx);
}

int addDdakji(int mRow, int mCol, int mSize, int mPlayer){
	int gr = mRow / M, gc = mCol / M;
	for (int r = gr; r <= min(MAXG, gr + 1); r++) for (int c = gc; c <= min(MAXG, gc + 1); c++){
		for (auto nx : m[r][c]) {
			if (overlap(nx, gCnt)) unionSet(nx, gCnt);
		}
		m[r][c].push_back(gCnt);
	}


	info[gCnt] = { mRow, mCol, mSize };
	p[gCnt] = mPlayer, r[gCnt] = 0;
	return 0;
}

int check(int mRow, int mCol){

	return 0;
}
#endif // 0

```
