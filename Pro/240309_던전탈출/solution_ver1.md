```cpp
#if 1
#define MAP_SIZE_MAX    350
#define INF 987654321
#include<vector>
#include<queue>
#include<algorithm>
#include<cstring>
using namespace std;

int(*map)[MAP_SIZE_MAX];
int N, M, maxStamina;
struct Info
{
	int gid, cost;
	bool operator == (const Info& info)const {
		return cost == info.cost && gid == info.gid;
	}
};
vector<Info> adj[205];    // 인접배열
struct Pos{	int r, c;}coord[205];

void init(int _N, int mMaxStamina, int mMap[MAP_SIZE_MAX][MAP_SIZE_MAX])
{
	map = mMap, N = _N, M = 0, maxStamina = mMaxStamina;
	for (auto& v : adj) v.clear();
}

struct Data { int r, c, cost; } que[350 * 350];
bool visit[350][350];
int dr[] = { -1,0,1,0 }, dc[] = { 0,1,0,-1 };
void addGate(int mGateID, int mRow, int mCol)
{
	M++; map[mRow][mCol] = mGateID + 1;
	coord[mGateID] = { mRow, mCol };
	int head = 0, tail = 0;
	memset(visit, 0, sizeof(visit));
	que[tail++] = { mRow, mCol, 0 };
	visit[mRow][mCol] = 1;

	while (head < tail) {
		Data& cur = que[head++];
		if (cur.cost == maxStamina) break;
		for (int i = 0; i < 4; i++) {
			int nr = cur.r + dr[i];
			int nc = cur.c + dc[i];
			int ncost = cur.cost + 1;

			if (visit[nr][nc]) continue;
			if (map[nr][nc] == 1) continue;

			que[tail++] = { nr,nc,ncost };
			visit[nr][nc] = 1;
			if (map[nr][nc] > 1) {
				int gateID = map[nr][nc] - 1;
				adj[mGateID].push_back({ gateID, ncost });
				adj[gateID].push_back({ mGateID, ncost });
			}
		}
	}
}

void removeGate(int mGateID)
{
	// 그래프 삭제
	for (auto p : adj[mGateID])
		adj[p.gid].erase(find(adj[p.gid].begin(), adj[p.gid].end(), Info{ mGateID, p.cost }));
	adj[mGateID].clear();
	// 맵 삭제
	map[coord[mGateID].r][coord[mGateID].c] = 0;
}

struct Data2 {
	int to, cost;
	bool operator < (const Data2& data2)const {
		return cost > data2.cost;
	}
};

priority_queue<Data2> pq;
int cost[205];
int getMinTime(int mStartGateID, int mEndGateID)
{
	pq = {}; pq.push({ mStartGateID, 0 });
	fill(cost, cost + M + 1, INF);
	cost[mStartGateID] = 0;
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		if (cur.cost > cost[cur.to]) continue;
		if (cur.to == mEndGateID) {
			return cost[cur.to];
		}
		for (auto nx : adj[cur.to]) {
			int nextCost = cur.cost + nx.cost;
			if (cost[nx.gid] > nextCost) {
				cost[nx.gid] = nextCost;
				pq.push({ nx.gid, cost[nx.gid] });
			}
		}
	}
	return -1;
}
#endif
```
