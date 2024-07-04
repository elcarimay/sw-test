```cpp
#include <vector>
#include <queue>
#include <math.h>
using namespace std;
using pii = pair<int, int>;

struct Result{ int row, col;};
struct Bacteria{ int id, size, time;};

int N;
int dr[] = { -1,0,1,0 }, dc[] = { 0,1,0,-1 };
int bacMap[205][205];
vector<pii> bacList[3005];
priority_queue<pii, vector<pii>, greater<>> removePQ; // time, mid
int visit[205][205], vcnt, head, tail;
pii que[200 * 200 + 5]; // row, col

struct Data
{
	int dist, row, col;
	bool operator<(const Data& r) const {
		if (dist != r.dist) return dist > r.dist;
		if (row != r.row) return row > r.row;
		return col > r.col;
	}
};
priority_queue<Data> pq;

void init(int N){
	::N = N, removePQ = {}, memset(bacMap, 0, sizeof(bacMap));
}

void remove(int time) {
	while (!removePQ.empty() && removePQ.top().first <= time) {
		int mid = removePQ.top().second;
		removePQ.pop();
		for (pii nx : bacList[mid]) bacMap[nx.first][nx.second] = 0;
		bacList[mid].clear();
	}
}

// size개 생성가능하면 1, 불가능하면 0
bool bfs(int size, int sr, int sc) {
	if (bacMap[sr][sc]) return 0;
	if (size == 1) return 1;
	head = tail = 0;
	que[tail++] = { sr,sc };
	visit[sr][sc] = ++vcnt;
	while (head < tail) {
		pii p = que[head++];
		for (int i = 0; i < 4; i++) {
			int r = p.first + dr[i], c = p.second + dc[i];
			if (r <1 || r>N || c<1 || c>N) continue;
			if (bacMap[r][c] || visit[r][c] == vcnt) continue;
			que[tail++] = { r,c };
			visit[r][c] = vcnt;
			if (tail == size) return 1;
		}
	}
	return 0;
}

Result put(int mid, int size, int sr, int sc) {
	pq = {}, pq.push({ 0, sr, sc });
	visit[sr][sc] = ++vcnt;
	while (1) {
		/* 우선순위 1개 선택 */
		Data d = pq.top(); pq.pop();
		bacMap[d.row][d.col] = mid;
		bacList[mid].push_back({ d.row, d.col });
		if (bacList[mid].size() == size) return { d.row, d.col };

		/* 인접한 공간 후보 등록 */
		for (int i = 0; i < 4; i++) {
			int r = d.row + dr[i], c = d.col + dc[i];
			if (r < 1 || r > N || c < 1 || c > N) continue;
			if (bacMap[r][c] || visit[r][c] == vcnt) continue;
			pq.push({ abs(r - sr) + abs(c - sc), r, c });
			visit[r][c] = vcnt;
		}
	}
}

Result putBacteria(int mTime, int mRow, int mCol, Bacteria mBac){
	bacList[mBac.id].clear();
	remove(mTime);
	if (bfs(mBac.size, mRow, mCol) == 0) return { 0,0 };
	removePQ.push({ mTime + mBac.time, mBac.id });
	return put(mBac.id, mBac.size, mRow, mCol);
}

int killBacteria(int mTime, int mRow, int mCol){
	remove(mTime);
	int mID = bacMap[mRow][mCol];
	for (pii p : bacList[mID]) bacMap[p.first][p.second] = 0;
	bacList[mID].clear();
	return mID;
}

int checkCell(int mTime, int mRow, int mCol){
	remove(mTime);
	return bacMap[mRow][mCol];
}
```
