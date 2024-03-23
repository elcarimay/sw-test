```cpp
#include <vector>
#include <queue>
using namespace std;

#define MAX_N 350
#define MAX_CHARGES 200
#define INF 999

struct Edge
{
	int to, cost;
	bool operator<(const Edge& edge) const{
		return cost > edge.cost;
	}
};

struct Stop
{
	int mRow, mCol;
	vector<Edge> stopList;
};

Stop stops[MAX_CHARGES] = {};
int map[MAX_N][MAX_N]; // int (*map)[MAX_N];
int n, R, visited[MAX_N][MAX_N];
int dr[] = { 1,0,-1,0 }, dc[] = {0,1,0,-1};

void bfs(int mRow, int mCol) {
	for (int i = 0; i < n; i++)
		for (int j = 0; j < n; j++) visited[i][j] = INF;
	visited[mRow][mCol] = 0;
	queue<Stop> Q; Q.push({ mRow, mCol });
	while (!Q.empty()) {
		auto cur = Q.front(); Q.pop();
		if (visited[cur.mRow][cur.mCol] >= R) break;
		for (int i = 0; i < 4; i++) {
			int nr = cur.mRow + dr[i], nc = cur.mCol + dc[i];
			if (0 <= nr && nr < n && 0 <= nc && nc < n) {
				if (visited[nr][nc] == INF && map[nr][nc] == 0) {
					visited[nr][nc] = visited[cur.mRow][cur.mCol] + 1;
					Q.push({ nr, nc });
				}
			}
		}
	}
}

void init(int N, int mRange, int mMap[MAX_N][MAX_N]){
	n = N; R = mRange;
	for (int i = 0; i < N; i++)
		for (int j = 0; j < N; j++) map[i][j] = mMap[i][j];
}

void add(int mID, int mRow, int mCol){
	stops[mID] = { mRow, mCol };
	stops[mID].stopList.clear();
	bfs(mRow, mCol);
	for (int i = 0; i < mID; i++) { // mID 이전까지 대여소에 대한 update도 진행함.
		if (visited[stops[i].mRow][stops[i].mCol] > R) continue;
		stops[i].stopList.push_back({ mID, visited[stops[i].mRow][stops[i].mCol] });
		stops[mID].stopList.push_back({ i, visited[stops[i].mRow][stops[i].mCol] });
	}
}

int distance(int mFrom, int mTo){
	int cost[MAX_CHARGES]; fill(cost, cost + MAX_CHARGES, INF);
	priority_queue<Edge> Q;
	Q.push({ mFrom, 0 });
	cost[mFrom] = 0;
	while (!Q.empty()) {
		auto cur = Q.top(); Q.pop();
		if (cur.cost > cost[cur.to]) continue;
		if (cur.to == mTo) return cost[cur.to];
		for (auto nx : stops[cur.to].stopList) {
			int nxCost = nx.cost + cost[cur.to];
			if (cost[nx.to] > nxCost) {
				cost[nx.to] = nxCost;
				Q.push({ nx.to, cost[nx.to] });
			}
		}
	}
	return -1;
}
```
