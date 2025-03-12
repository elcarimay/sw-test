```cpp
#if 1
// [다익스트라 변형] 다익스트라 형식이지만 BFS느낌으로 지나가면서 그 경로내에 최소 비용을 update함.
#include <vector>
#include <queue>
using namespace std;

#define MAXN 1003
#define INF 987654321

struct Edge {
	int to, cost;
	bool operator<(const Edge& r)const {
		return cost< r.cost;
	}
};

vector<Edge> adj[MAXN];
void add(int sCity, int eCity, int mLimit) {
	adj[sCity].push_back({ eCity, mLimit });
}

int N;
void init(int N, int K, int sCity[], int eCity[], int mLimit[]) {
	::N = N;
	for (int i = 0; i < N; i++) adj[i].clear();
	for (int i = 0; i < K; i++) add(sCity[i], eCity[i], mLimit[i]);
}

int cost[MAXN];
int calculate(int sCity, int eCity) {
	for (int i = 0; i < N; i++) cost[i] = -1;
	priority_queue<Edge> pq;
	pq.push({ sCity, INF });
	cost[sCity] = INF;
	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.cost < cost[cur.to]) continue;
		if (cur.to == eCity) return cost[cur.to];
		for (Edge nx : adj[cur.to]) {
			int nextCost = min(cur.cost, nx.cost);
			if (cost[nx.to] < nextCost) {
				cost[nx.to] = nextCost;
				pq.push({ nx.to, cost[nx.to] });
			}
		}
	}
	return -1;
}
#endif // 1

```
