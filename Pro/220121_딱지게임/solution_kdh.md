```cpp
#if 1
// Union-Find Problem
// 각 그룹별 ID가 필요없고 그룹ID와 그 그룹에 속하는 ID의 갯수만 관리 -> cnt[MAXN]
// 그룹ID의 부모인 플레이어 1, 2에 대한 저장 -> pid[MAXN]
// 각 플레이어별 갯수 저장 -> pCnt[MAXN]
#define MAXN 20003 // 한게임은 최대 10,000턴인데 1, 2플레이어가 한번씩 진행하므로 20,000턴임
#include <vector>
using namespace std;

struct Info {
	int r, c, len;
}info[MAXN];
vector<int> m[13][13];
int M, idCnt, p[MAXN], r[MAXN], MAXG, cnt[MAXN], pid[MAXN]; // cnt: 그룹 딱지갯수, pid: 그룹 소유 플레이어 번호
int pCnt[3]; // 플레이어 딱지 개수
void init(int N, int M){
	::M = M, idCnt = 0,	MAXG = N / M;
	for (int i = 1; i <= 2; i++) cnt[i] = pCnt[i] = 0;
	for (int i = 0; i <= 10; i++) for (int j = 0; j <= 10; j++)
		m[i][j].clear();
}

bool overlap(int a, int b) {
	int a_sr = info[a].r, a_er = info[a].r + info[a].len - 1;
	int b_sr = info[b].r, b_er = info[b].r + info[b].len - 1;
	int a_sc = info[a].c, a_ec = info[a].c + info[a].len - 1;
	int b_sc = info[b].c, b_ec = info[b].c + info[b].len - 1;
	return !(a_er < b_sr || b_er < a_sr || a_ec < b_sc || b_ec < a_sc);
}

int find(int x) {
	if (p[x] != x) return p[x] = find(p[x]);
	return p[x];
}

void unionSet(int x, int y) {
	x = find(x), y = find(y);
	if (x == y) return;
	pCnt[pid[x]] -= cnt[x], pCnt[pid[y]] += cnt[x];
	if (r[x] < r[y]) p[x] = y, cnt[y] += cnt[x];
	else p[y] = x, cnt[x] += cnt[y], pid[x] = pid[y];
	if (r[x] == r[y]) r[x]++;
}

int addDdakji(int mRow, int mCol, int mSize, int mPlayer){
	info[idCnt] = { mRow, mCol, mSize };
	p[idCnt] = idCnt, r[idCnt] = 0, pid[idCnt] = mPlayer, cnt[idCnt] = 1, pCnt[mPlayer]++;
	for (int r = mRow / M; r <= (mRow + mSize - 1) / M; r++) for (int c = mCol / M; c <= (mCol + mSize - 1) / M; c++){
		for (int nx : m[r][c]) if (overlap(nx, idCnt))	unionSet(nx, idCnt);
		m[r][c].push_back(idCnt);
	}
	idCnt++;
	return pCnt[mPlayer];
}

int check(int mRow, int mCol){
	for (int nx : m[mRow / M][mCol / M]) {
		int sr = info[nx].r, er = info[nx].r + info[nx].len - 1;
		int sc = info[nx].c, ec = info[nx].c + info[nx].len - 1;
		if (sr <= mRow && mRow <= er && sc <= mCol && mCol <= ec) return pid[find(nx)];
	}
	return 0;
}
#endif // 0
```
