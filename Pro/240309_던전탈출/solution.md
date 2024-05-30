```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#define MAP_SIZE_MAX	350
#include <vector>
#include <queue>
#include <algorithm>
#include <cstring>
using namespace std;

struct Pos {
	int r, c;
}coord[205];

struct Data {
	int to, cost;
	bool operator<(const Data& r)const {
		return cost > r.cost;
	}
	bool operator==(const Data& r)const {
		return to == r.to && cost == r.cost;
	}
};

int N, M, maxStamina; // 맵크기, 게이트 최대번호, 최대 체력
int(*A)[MAP_SIZE_MAX]; // 맵(0:길, 1:기둥, 2~:gateID+1)
vector<Data> adj[205];

void init(int N, int mMaxStamina, int mMap[MAP_SIZE_MAX][MAP_SIZE_MAX]){
	::N = N, M = 0, A = mMap;
	maxStamina = mMaxStamina;
	for (int i = 0; i < 205; i++) adj[i].clear();
}

struct Info {
	int r, c, d;
}que[MAP_SIZE_MAX* MAP_SIZE_MAX];
bool visit[MAP_SIZE_MAX][MAP_SIZE_MAX];
int dr[] = { -1,0,1,0 }, dc[] = { 0,1,0,-1 };

void addGate(int mGateID, int mRow, int mCol){
	M++;
	A[mRow][mCol] = mGateID + 1;
	coord[mGateID] = { mRow, mCol };
	int head = 0, tail = 0;
	memset(visit, 0, sizeof(visit));
	que[tail++] = { mRow, mCol, 0 };
	visit[mRow][mCol] = 1;
	while (head < tail) {
		Info& cur = que[head++];
		if (cur.d == maxStamina) break;

		for (int i = 0; i < 4; i++) {
			int r = cur.r + dr[i];
			int c = cur.c + dc[i];
			int d = cur.d + 1;

			if (visit[r][c] || A[r][c] == 1) continue;
			que[tail++] = { r,c,d };
			visit[r][c] = 1;
			if (A[r][c] > 1) {
				int mGateID2 = A[r][c] - 1;
				adj[mGateID].push_back({ mGateID2,d });
				adj[mGateID2].push_back({ mGateID,d });
			}
		}
	}
}

void removeGate(int mGateID){
	for (auto nx : adj[mGateID]) {
		adj[nx.to].erase(find(adj[nx.to].begin(), adj[nx.to].end(), Data{ mGateID,nx.cost }));
	}
	adj[mGateID].clear();
	A[coord[mGateID].r][coord[mGateID].c] = 0;
}

// Dijkstra
priority_queue<Data> pq;
int cost[205];
int getMinTime(int mStartGateID, int mEndGateID){
	pq = {}; pq.push({ mStartGateID,0 });
	fill(cost, cost + M + 1, 1e9);
	cost[mStartGateID] = 0;
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		if (cur.cost > cost[cur.to]) continue;
		if (cur.to == mEndGateID) return cost[cur.to];
		for (auto nx : adj[cur.to]) {
			int nextCost = cost[cur.to] + nx.cost;
			if (cost[nx.to] > nextCost) {
				cost[nx.to] = nextCost;
				pq.push({ nx.to, cost[nx.to] });
			}
		}
	}
	return -1;
}
#endif
```
