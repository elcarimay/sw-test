```cpp
#if 1
#include <vector>
#include <queue>
using namespace std;

#define MAX_N		350
#define MAX_CHARGES	200
#define INF 999

struct Data
{
	int mDist, mID;
	bool operator<(const Data& data) const{ return mDist > data.mDist; }
};

priority_queue<Data> pq;

struct EVR
{
	int r, c;
	vector<Data> evrList; // dist, id
};

EVR evr[MAX_CHARGES] = {};
int (*map)[MAX_N], dist[MAX_N][MAX_N], mSize, range;

queue<EVR> Q;
EVR dr[] = { {1,0},{-1,0}, {0,1},{0,-1} };

void init(int N, int mRange, int mMap[MAX_N][MAX_N]) {
	mSize = N;
	range = mRange; // 충전후 전기차의 이동가능거리(5 < mRange < 100)
	map = mMap;		// 2D array 대입
}

void bfs(int r, int c) {
	while (!Q.empty()) { Q.pop(); }
	for (int i = 0; i < mSize; i++)
		for (int j = 0; j < mSize; j++) dist[i][j] = INF;
	Q.push({ r,c }); dist[r][c] = 0;
	while (!Q.empty()) {
		auto cur = Q.front(); Q.pop();
		if (dist[cur.r][cur.c] >= range) break;
		for (auto dir:dr) {
			int nr = cur.r + dir.r;
			int nc = cur.c + dir.c;
			if (nr < 0 || nc < 0 || nr >= mSize || nc >= mSize) continue; //영역 벗어날 때
			if (dist[nr][nc] != INF || map[nr][nc] == 1) continue;
			dist[nr][nc] = dist[cur.r][cur.c] + 1;
			Q.push({ nr,nc });
		}
	}
}

void add(int mID, int mRow, int mCol) {
	evr[mID] = { mRow, mCol };
	evr[mID].evrList.clear();
	bfs(mRow, mCol);
	for (int i = 0; i < mID; i++)
	{
		if (dist[evr[i].r][evr[i].c] > range) continue;
		evr[i].evrList.push_back({ dist[evr[i].r][evr[i].c],mID });
		evr[mID].evrList.push_back({ dist[evr[i].r][evr[i].c],i });
	}
}

int distance(int mFrom, int mTo) {
	vector<int> dist(MAX_CHARGES, INF);
	while (!pq.empty()) pq.pop();
	pq.push({ 0, mFrom });
	dist[mFrom] = 0;
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		if (cur.mDist > dist[cur.mID]) continue;
		if (cur.mID == mTo) return dist[cur.mID];
		for (auto n : evr[cur.mID].evrList)
			if (dist[n.mID] > dist[cur.mID] + n.mDist) {
				dist[n.mID] = dist[cur.mID] + n.mDist;
				pq.push({ dist[n.mID], n.mID });
			}
	}
	return -1;
}
#endif
```
