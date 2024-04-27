```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <queue>
#include <algorithm>
#include <cmath>
using namespace std;

#define INF 9999

struct Edge
{
	int to, cost;
	bool operator<(const Edge& edge) const {
		return cost > edge.cost;
	}
	bool operator==(const Edge& edge) const {
		return to == edge.to;
	}
};
int N, M, a, b, t;
int cost[30050];
vector<Edge> gadj[30050];
priority_queue<Edge> pq;
int dijkstra(vector<Edge> v[], int s, int e, int n = 30) {
	fill(cost + 1, cost + n + 1, INF); cost[s] = 0;
	pq = {}; pq.push({ s, 0 });
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		if (cur.cost > cost[cur.to]) continue;
		if (cur.to == e) return cost[cur.to];
		for (auto nx : v[cur.to]) {
			int nxCost = nx.cost + cost[cur.to];
			if (cost[nx.to] > nxCost)
				pq.push({ nx.to, cost[nx.to] = nxCost });
		}
	}
	return 0;
}

vector<Edge> ladj[305][50];

void removeEdge(vector<Edge> v[],int a, int b) {
	for (int i = 0; i < v[a].size(); i++)
		if (v[a][i].to == b) {
			v[a].erase(v[a].begin() + i); break;
		}
	for (int i = 0; i < v[b].size(); i++)
		if (v[b][i].to== a) {
			v[b].erase(v[b].begin() + i); break;
		}
}

void update(int id) { // 그룹 id에 해당하는 local계산후 global update
	for (int i = 1; i <= 3; i++) {
		a = i, b = (i % 3) + 1;
		int dist = dijkstra(ladj[id], a, b);
		if (dist) {
			removeEdge(gadj, id * 100 + a, id * 100 + b);
			gadj[id * 100 + a].push_back({ id * 100 + b,dist });
			gadj[id * 100 + b].push_back({ id * 100 + a,dist });
		}
	}
}

void init(int _N, int K, int mNodeA[], int mNodeB[], int mTime[])
{
	N = _N, M = N * 100 + 30;
	for (int i = 1; i <= M; i++) gadj[i].clear();
	for (int i = 1; i <= N; i++)
		for (int j = 1; j <= 30; j++) ladj[i][j].clear();
	for (int i = 0; i < K; i++) {
		a = mNodeA[i], b = mNodeB[i], t = mTime[i];
		if (a / 100 == b / 100) {
			ladj[a / 100][a % 100].push_back({ b % 100,t });
			ladj[a / 100][b % 100].push_back({ a % 100,t });
		}
		else {
			gadj[mNodeA[i]].push_back({ mNodeB[i],t });
			gadj[mNodeB[i]].push_back({ mNodeA[i],t });
		}
	}
	for (int i = 1; i <= N; i++) update(i);
}
void addLine(int mNodeA, int mNodeB, int mTime)
{
	a = mNodeA, b = mNodeB, t = mTime;
	if (a / 100 == b / 100) {
		ladj[a / 100][a % 100].push_back({ b % 100,t });
		ladj[a / 100][b % 100].push_back({ a % 100,t });
		update(a / 100);
	}
	else {
		gadj[mNodeA].push_back({ mNodeB,t });
		gadj[mNodeB].push_back({ mNodeA,t });
	}
}

void removeLine(int mNodeA, int mNodeB)
{
	a = mNodeA, b = mNodeB;
	if (a / 100 == b / 100) {
		removeEdge(ladj[a / 100], a % 100, b % 100);
		update(a / 100);
	}
	else removeEdge(gadj, a, b);
}

int checkTime(int mNodeA, int mNodeB)
{
	return dijkstra(gadj, mNodeA, mNodeB, M);;
}
#endif
```
