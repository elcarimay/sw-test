```cpp
#define _CRT_NO_SECURE_WARNGINS
#include <queue>
#include <vector>
using namespace std;

#define INF 999
#define MAX_GATES 200
#define MAP_SIZE_MAX 350

struct Data
{
	int to, cost;
	bool operator<(const Data& data)const {
		return cost > data.cost;
	}
};

struct Gate
{
	int x, y;
	vector<Data> gateList;
}gate[MAX_GATES];

queue<Gate> Q;
priority_queue<Data> PQ;
int dx[] = { 0,-1,0,1 }, dy[] = { 1, 0,-1,0 };
int map[MAP_SIZE_MAX][MAP_SIZE_MAX], MaxStamina;
int visited[MAP_SIZE_MAX][MAP_SIZE_MAX], n;

void bfs(int sx, int sy) {
	while (!Q.empty()) Q.pop();
	for (int i = 0;i < MAP_SIZE_MAX;i++)
		for (int j = 0;j < MAP_SIZE_MAX;j++)
			visited[i][j] = INF;
	visited[sx][sy] = 0;
	Q.push({ sx,sy });
	while (!Q.empty()) {
		auto cur = Q.front(); Q.pop();
		if (visited[cur.x][cur.y] >= MaxStamina) break;
		for (int i = 0; i < 4;i++) {
			int nx = cur.x + dx[i], ny = cur.y + dy[i];
			if (0 <= nx && nx < n && 0 <= ny && ny < n)
				if (map[nx][ny] == 0 && visited[nx][ny] == INF) {
					visited[nx][ny] = visited[cur.x][cur.y] + 1;
					Q.push({ nx, ny });
				}
		}

	}
}

void init(int N, int mMaxStamina, int mMap[MAP_SIZE_MAX][MAP_SIZE_MAX]) {
	n = N; MaxStamina = mMaxStamina;
	for (int i = 0;i < n;i++)
		for (int j = 0;j < n;j++)
			map[i][j] = mMap[i][j];
}

void addGate(int mGateID, int mRow, int mCol) {
	mGateID--;
	gate[mGateID] = { mRow, mCol };
	gate[mGateID].gateList.clear();
	bfs(mRow, mCol);
	for (int i = 0;i < mGateID;i++) {
		if (visited[gate[i].x][gate[i].y] > MaxStamina) continue;
		gate[i].gateList.push_back({ mGateID, visited[gate[i].x][gate[i].y] });
		gate[mGateID].gateList.push_back({ i, visited[gate[i].x][gate[i].y] });
	}
}

void removeGate(int mGateID) {
	gate[--mGateID] = {};
}

int getMinTime(int mStartGateID, int mEndGateID) {
	vector<int> cost(MAX_GATES, INF);
	mStartGateID--; mEndGateID--;
	while (!PQ.empty()) PQ.pop();
	cost[mStartGateID] = 0;
	PQ.push({ mStartGateID, 0 });
	while (!PQ.empty()) {
		auto cur = PQ.top();PQ.pop();
		if (cur.cost > cost[cur.to]) continue;
		if (cur.to == mEndGateID) return cost[cur.to];
		for (auto nx : gate[cur.to].gateList) {
			int nextCost = nx.cost + cost[cur.to];
			if (cost[nx.to] > nextCost) {
				cost[nx.to] = nextCost;
				PQ.push({ nx.to, cost[nx.to] });
			}
		}
	}
	return -1;
}
```
