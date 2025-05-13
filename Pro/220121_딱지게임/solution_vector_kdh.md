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
int M, idCnt, p[MAXN], r[MAXN], cnt[MAXN], pid[MAXN]; // pid: group별로 Player1, 2중 어디에 속하는지
int pCnt[3];
void init(int N, int M) {
	::M = M, idCnt = pCnt[1] = pCnt[2] = 0;
	for (int r = 0; r <= 10; r++) for (int c = 0; c <= 10; c++) m[r][c].clear();
}

bool overlap(int x, int y) {
	int x_sr = info[x].r, x_er = x_sr + info[x].len - 1;
	int x_sc = info[x].c, x_ec = x_sc + info[x].len - 1;
	int y_sr = info[y].r, y_er = y_sr + info[y].len - 1;
	int y_sc = info[y].c, y_ec = y_sc + info[y].len - 1;
	return !(y_er < x_sr || x_er < y_sr || x_ec < y_sc || y_ec < x_sc);
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
	if (r[x] == r[y])  r[x]++;
}

int addDdakji(int mRow, int mCol, int mSize, int mPlayer) {
	info[idCnt] = { mRow, mCol, mSize };
	p[idCnt] = idCnt, r[idCnt] = 0, cnt[idCnt] = 1, pid[idCnt] = mPlayer, pCnt[mPlayer]++;
	for (int r = mRow / M; r <= (mRow + mSize - 1) / M; r++) for (int c = mCol / M; c <= (mCol + mSize - 1) / M; c++) {
		for (int nid : m[r][c])
			if (overlap(nid, idCnt)) unionSet(nid, idCnt);
		m[r][c].push_back(idCnt);
	}
	idCnt++;
	return pCnt[mPlayer];
}

int check(int mRow, int mCol) {
	for (int nid : m[mRow / M][mCol / M])
		if (info[nid].r <= mRow && mRow <= info[nid].r + info[nid].len - 1 && info[nid].c <= mCol && mCol <= info[nid].c + info[nid].len - 1)
			return pid[find(nid)];
	return 0;
}
#endif // 0
```
