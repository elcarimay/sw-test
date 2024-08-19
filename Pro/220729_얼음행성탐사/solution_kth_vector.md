```cpp
#if 1 // 72 ms
/*
* vector, sort, lower_bound
* 초기 vector에 양쪽 경계 등록 { -1, C or R }
* 현 위치와 이동하는 위치 사이에 목표 지점 있으면 종료
*/
#include<vector>
#include<algorithm>
using namespace std;
vector<int> rows[700], cols[10000];	// 구조물 관리: rows[r] = {col,...}   , cols[c] = {row,..}
int visit[700][10000], vcnt;		// BFS 방문 처리: visit[r][c] = vcnt
int R, C;

void init(int R, int C, int N, int sRow[30000], int sCol[30000]) {
	::R = R, ::C = C;
	for (int i = 0; i < R; i++) rows[i] = { -1,C };
	for (int i = 0; i < C; i++) cols[i] = { -1,R };
	for (int i = 0; i < N; i++) {
		rows[sRow[i]].push_back(sCol[i]);
		cols[sCol[i]].push_back(sRow[i]);
	}
	for (int i = 0; i < R; i++) sort(rows[i].begin(), rows[i].end());
	for (int i = 0; i < C; i++) sort(cols[i].begin(), cols[i].end());
}

int head, tail;
struct Data { int r, c, d; } que[120000]; // 방문 가능 위치 구조 인접4곳 = 30,000*4

void push(int r, int c, int d) {
	if (visit[r][c] == vcnt) return;
	que[tail++] = { r,c,d };
	visit[r][c] = vcnt;
}

int minDamage(int sr, int sc, int er, int ec) {
	vcnt++;
	head = tail = 0;
	push(sr, sc, 0);
	while (head < tail) {
		int r = que[head].r;
		int c = que[head].c;
		int d = que[head++].d;

		auto rit = lower_bound(cols[c].begin(), cols[c].end(), r); // (r, c) 바로 아래쪽 구조물 선택
		int r2 = *rit, r1 = *--rit; // r1: 위 구조물 row, r2: 아래 구조물 row
		if (c == ec && r1 < er && er < r2) return d; // 거치는 곳에 목표 지점이 있는 경우
		if (r1 != -1) push(r1 + 1, c, d + 1);
		if (r2 != R) push(r2 - 1, c, d + 1);

		auto cit = lower_bound(rows[r].begin(), rows[r].end(), c); // (r, c) 바로 오른쪽 구조물 선택
		int c2 = *cit, c1 = *--cit; // c1: 오른쪽 구조물 col, c2: 왼쪽 구조물 col
		if (r == er && c1 < ec && ec < c2) return d; // 거치는 곳에 목표 지점이 있는 경우
		if (c1 != -1) push(r, c1 + 1, d + 1);
		if (c2 != C) push(r, c2 - 1, d + 1);
	}
	return -1;
}
#endif // 1

```
