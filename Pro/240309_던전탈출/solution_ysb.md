```cpp
#include<vector>
#include<queue>
using namespace std;
using pii = pair<int, int>;

#define MAP_SIZE_MAX	350
#define INF	987654321

int N, R;
int(*M)[MAP_SIZE_MAX];
int dist[MAP_SIZE_MAX][MAP_SIZE_MAX];
struct Gate {
	int r, c;
	vector<pii>gList;
	bool removed;
}g[201];
int dr[] = { -1, 0, 1, 0 }, dc[] = { 0, 1, 0, -1 };

void init(int N, int mMaxStamina, int mMap[MAP_SIZE_MAX][MAP_SIZE_MAX]) {
	::N = N, R = mMaxStamina, M = mMap;
}

void bfs(int sr, int sc) {
	queue<pii> Q;
	for (int i = 0; i < N; i++)
		for (int j = 0; j < N; j++)
			dist[i][j] = INF;
	Q.push({ sr, sc });
	dist[sr][sc] = 0; //start
	while (Q.size()) {
		auto c = Q.front();
		Q.pop();
		if (dist[c.first][c.second] >= R) break;
		for (int i = 0; i < 4; i++) {
			int nr = c.first + dr[i], nc = c.second + dc[i];
			if (nr < 0 || nc < 0 || nr >= N || nc >= N) continue; //영역 벗어날 때
			if (dist[nr][nc] != INF || M[nr][nc] == 1) continue; // 방문을 했거나, 장애물이 있을 때
			dist[nr][nc] = dist[c.first][c.second] + 1;
			Q.push({ nr, nc });
		}
	}
}

void addGate(int mGateID, int mRow, int mCol) {
	g[mGateID] = { mRow, mCol };
	g[mGateID].gList.clear();
	bfs(mRow, mCol);
	for (int i = 1; i < mGateID; i++) {
		if (g[i].removed) continue;
		if (dist[g[i].r][g[i].c] > R) continue;
		g[mGateID].gList.push_back({ dist[g[i].r][g[i].c], i });
		g[i].gList.push_back({ dist[g[i].r][g[i].c], mGateID });
	}
}

void removeGate(int mGateID) {
	for (pii p : g[mGateID].gList) {
		int cost = p.first;
		int x = p.second;
		g[x].gList.erase(find(g[x].gList.begin(), g[x].gList.end(), pii{ cost, mGateID }));
	}
	g[mGateID].gList.clear();
	g[mGateID].removed = true;
}
priority_queue<pii> pq;
int getMinTime(int mStartGateID, int mEndGateID) {
	vector<int>cost(201, INF);
	pq = {};
	pq.push({ 0, mStartGateID });
	cost[mStartGateID] = 0;
	while (pq.size()) {
		auto c = pq.top();
		pq.pop();
		if (-c.first > cost[c.second]) continue;
		if (c.second == mEndGateID)
			return -c.first;
		for (auto n : g[c.second].gList) {
			if (cost[n.second] > -c.first + n.first) {
				cost[n.second] = -c.first + n.first;
				pq.push({ -cost[n.second], n.second });
			}
		}
	}
	return -1;
}
```
