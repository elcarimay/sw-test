```cpp
#if 0 // 105 ms
/*
* set, lower_bound
* 목표 위치 set에 등록 후, 그 위치 만나면 종료, set에서 삭제
*/
#include<set>
using namespace std;
set<int> rset[700], cset[10000];	// 구조물 관리: rset[row] = {col,...}   , cset[col] = {row,..}
int visit[700][10000], vcnt;		// BFS 방문 처리: visit[r][c] = vcnt

void init(int R, int C, int N, int sRow[30000], int sCol[30000])
{
	for (int i = 0; i < R; i++) rset[i].clear();
	for (int i = 0; i < C; i++) cset[i].clear();
	for (int i = 0; i < N; i++) {
		rset[sRow[i]].insert(sCol[i]);
		cset[sCol[i]].insert(sRow[i]);
	}
}

int head, tail;
struct Data { int r, c, d; } que[120000];	// {row, col, damage}
											// 방문 가능 위치 구조물 인접 4곳 = 30,000 * 4
void push(int r, int c, int d) {
	if (visit[r][c] == vcnt) return;
	que[tail++] = { r,c,d };
	visit[r][c] = vcnt;
}

int bfs(int sr, int sc, int er, int ec) {
	vcnt++;
	head = tail = 0;
	push(sr, sc, 0);
	while (head < tail) {
		int r = que[head].r;
		int c = que[head].c;
		int d = que[head++].d;

		auto rit = cset[c].lower_bound(r);			// (r,c) 바로 아래 구조물 선택
		if (rit != cset[c].end()) {					// 아래 구조물 있는 경우
			if (*rit == er && c == ec) return d;	// 목표 지점인 경우
			push(*rit - 1, c, d + 1);
		}
		if (rit != cset[c].begin()) {				// 위 구조물 있는 경우
			if (*--rit == er && c == ec) return d;	// 목표 지점인 경우
			push(*rit + 1, c, d + 1);
		}

		auto cit = rset[r].lower_bound(c);			// (r,c) 바로 오른쪽 구조물 선택
		if (cit != rset[r].end()) {					// 오른쪽 구조물 있는 경우
			if (r == er && *cit == ec) return d;	// 목표 지점인 경우
			push(r, *cit - 1, d + 1);
		}
		if (cit != rset[r].begin()) {				// 왼쪽 구조물 있는 경우
			if (*--cit == ec && r == er) return d;	// 묙표 지점인 경우
			push(r, *cit + 1, d + 1);
		}
	}
	return -1;
}

int minDamage(int sr, int sc, int er, int ec)
{
	rset[er].insert(ec);				// 목표 지점 구조물로 등록
	cset[ec].insert(er);
	int ret = bfs(sr, sc, er, ec);
	rset[er].erase(ec);					// 목표 지점 구조물에서 제거
	cset[ec].erase(er);
	return ret;
}
#endif
```
