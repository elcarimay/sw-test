```cpp
#if 1 // 112 ms
/*
* set , lower_bound
* 초기 set에 양쪽 경계 등록 { -1, C or R }
* 현 위치와 이동하는 위치 사이에 목표 지점 있으면 종료
*/
#include<set>
using namespace std;

int R, C;
set<int> rset[700], cset[10000];
int visit[700][10000], vcnt;

void init(int R, int C, int N, int sRow[30000], int sCol[30000])
{
	::R = R, ::C = C;
	for (int i = 0; i < R; i++) rset[i] = { -1, C };
	for (int i = 0; i < C; i++) cset[i] = { -1, R };
	for (int i = 0; i < N; i++) {
		rset[sRow[i]].insert(sCol[i]);
		cset[sCol[i]].insert(sRow[i]);
	}
}

int head, tail;
struct Data { int r, c, d; } que[120000];

void push(int r, int c, int d) {
	if (visit[r][c] == vcnt) return;
	que[tail++] = { r,c,d };
	visit[r][c] = vcnt;
}

int minDamage(int sr, int sc, int er, int ec)
{
	vcnt++;
	head = tail = 0;
	push(sr, sc, 0);
	while (head < tail) {
		int r = que[head].r;
		int c = que[head].c;
		int d = que[head++].d;

		auto rit = cset[c].lower_bound(r);
		int r2 = *rit, r1 = *--rit;
		if (c == ec && r1 < er && er < r2) return d;
		if (r1 != -1) push(r1 + 1, c, d + 1);
		if (r2 != R) push(r2 - 1, c, d + 1);


		auto cit = rset[r].lower_bound(c);
		int c2 = *cit, c1 = *--cit;
		if (r == er && c1 < ec && ec < c2) return d;
		if (c1 != -1) push(r, c1 + 1, d + 1);
		if (c2 != C) push(r, c2 - 1, d + 1);
	}
	return -1;
}
#endif // 1 // 112 ms

```
