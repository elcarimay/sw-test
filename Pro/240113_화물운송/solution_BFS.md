```cpp
#if 1
#include <vector>
#include <queue>
using namespace std;

#define INF 987654321
#define MAX_N 1005 // 도시의 개수
#define MAX 4000 // 도로의 개수
#define MAX_mLimit 30000 // 최대중량

struct Edge
{
	int to, cost;
	bool operator<(const Edge& edge)const {
		return cost < edge.cost;
	}
};

int n;
vector<Edge> adj[MAX];
void init(int N, int K, int sCity[], int eCity[], int mLimit[]) {
	n = N;
	for (int i = 0; i < n; i++) adj[i].clear();
	for (int i = 0; i < K; i++)
		adj[sCity[i]].push_back({ eCity[i], mLimit[i] });
}

void add(int sCity, int eCity, int mLimit) {
	adj[sCity].push_back({ eCity, mLimit });
	return;
}

// 단방향이므로 한번만 지나가면 됨.
// 비용이 높은 경로로 지나가면서 가장 낮은 비용을 반환하면 됨.
// BFS only버전(120 ms)
int calculate1(int sCity, int eCity) {
	bool visit[MAX_N]; int min_v = INF;
	fill(visit, visit + n + 1, false);
	priority_queue<Edge> pq;
	pq.push({ sCity,0 });
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		if (min_v > cur.cost && cur.to != sCity) min_v = cur.cost;
		visit[cur.to] = true;
		if (cur.to == eCity) return min_v;
		for (auto nx : adj[cur.to]) {
			if (visit[nx.to] != true) pq.push(nx);
		}
	}
	return -1;
}

bool bfs(int sCity, int eCity, int limit) {
	bool visit[MAX_N];
	fill(visit, visit + MAX_N, false);
	queue<Edge> pq;
	pq.push({ sCity,0 });
	while (!pq.empty()) {
		auto cur = pq.front(); pq.pop();
		for (auto nx : adj[cur.to]) {
			if (nx.cost < limit) continue;
			if (visit[nx.to]) continue;
			if (nx.to == eCity) return true;
			visit[nx.to] = true;
			pq.push({ nx.to,nx.cost });
		}
	}
	return visit[eCity];
}

int binary_search(int low, int high, int sCity, int eCity) {
	int mid, res = -1;
	while (low <= high) {
		mid = (low + high) / 2;
		if (bfs(sCity, eCity, mid)) {
			low = mid + 1; res = mid;
		}
		else high = mid - 1;
	}
	return res;
}

// 일정비용(mid)보다 큰 도로는 bfs시 제외하면서 결정문제로 만듬.
// BFS + binary search버전( 300 ms)
int calculate2(int sCity, int eCity) {
	return binary_search(1, MAX_mLimit, sCity, eCity);
}

// 초기값을 INF로 정하고 현재비용과 다음 비용과의 최소값을 갱신해주고 다음지점의 비용값이 갱신값보다 작으면 update함.
// dijkstra 버전 ( 105 ms)
int cost[MAX_N];
int calculate(int sCity, int eCity) {
	fill(cost, cost + n + 1, 0);
	cost[sCity] = INF;
	priority_queue<Edge> pq;
	pq.push({ sCity,INF });
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		if (cur.cost < cost[cur.to]) continue;
		if (cur.to == eCity) return cost[cur.to];
		for (auto nx : adj[cur.to]) {
			int nextCost = min(cur.cost, nx.cost);
			if (cost[nx.to] < nextCost) {
				pq.push({ nx.to, cost[nx.to] = nextCost });
			}
		}
	}
	return -1;
}
#endif
```
