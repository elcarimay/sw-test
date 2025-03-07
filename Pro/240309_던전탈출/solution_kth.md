```cpp
// 게이트 추가시 bfs로 스태미나로 갈수 있는 거리에 있는 게이트를 추가하여 인접배열 생성
// 상기 인접배열로 다익스트라를 실행하여 목적지까지 최단거리를 계산
#include <vector>
#include <algorithm>
#include <string.h>
#include <queue>
using namespace std;

#define MAP_SIZE_MAX	350
#define MAXN 203
#define INF 987654321

struct Pos {
	int r, c;
}coord[MAXN];

struct Edge {
	int to, dist;
	bool operator<(const Edge& r)const {
		return dist > r.dist;
	}
	bool operator==(const Edge& r)const {
		return to == r.to && dist == r.dist;
	}
};

int N, S, cnt; int(*M)[MAP_SIZE_MAX];
vector<Edge> adj[MAXN];
void init(int N, int mMaxStamina, int mMap[MAP_SIZE_MAX][MAP_SIZE_MAX]){
	::N = N, S = mMaxStamina, M = mMap, cnt = 0;
	for (auto& v : adj) v.clear();
}

int dr[] = { 0,-1,0,1 }, dc[] = { 1,0,-1,0 }, head, tail;
struct Data {
	int r, c, dist;
} que[MAP_SIZE_MAX * MAP_SIZE_MAX];
bool visit[MAP_SIZE_MAX][MAP_SIZE_MAX];
void addGate(int mGateID, int mRow, int mCol){
	cnt++, head = tail = 0; coord[mGateID] = { mRow, mCol };
	M[mRow][mCol] = mGateID + 1;
	que[tail++] = { mRow, mCol, 0 };
	memset(visit, 0, sizeof(visit));
	visit[mRow][mCol] = true;
	while (head < tail) {
		Data cur = que[head++];
		if (cur.dist == S) break;
		for (int i = 0; i < 4; i++) {
			int nr = cur.r + dr[i], nc = cur.c + dc[i], ndist = cur.dist + 1;
			if (visit[nr][nc]) continue; visit[nr][nc] = true;
			if (M[nr][nc] == 1) continue;
			que[tail++] = { nr, nc, ndist };
			if (M[nr][nc] > 1) {
				adj[mGateID].push_back({ M[nr][nc] - 1, ndist });
				adj[M[nr][nc] - 1].push_back({ mGateID, ndist });
			}
		}
	}
}

void removeGate(int mGateID){
	for (Edge nx : adj[mGateID])
		adj[nx.to].erase(find(adj[nx.to].begin(), adj[nx.to].end(), Edge{ mGateID, nx.dist }));
	adj[mGateID].clear();
	M[coord[mGateID].r][coord[mGateID].c] = 0;
}

int cost[MAXN];
int getMinTime(int mStartGateID, int mEndGateID){
	for (int i = 0; i <= cnt; i++) cost[i] = INF;
	priority_queue<Edge> pq;
	pq.push({ mStartGateID, 0 });
	cost[mStartGateID] = 0;
	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.dist > cost[cur.to]) continue;
		if (cur.to == mEndGateID) return cost[cur.to];
		for (Edge nx : adj[cur.to]) {
			int nextDist = cost[cur.to] + nx.dist;
			if (cost[nx.to] > nextDist) 
				pq.push({ nx.to, cost[nx.to] = nextDist });
		}
	}
	return -1;
}
```
