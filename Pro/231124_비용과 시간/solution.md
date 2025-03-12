```cpp
// 다익스트라로 모든 비용을 업데이트 하는데 비용을 계속 더해서 Q에 남게함.
// 남아있는 Q들 중에서 M값내에 있는 Q만 선별해서 반환함.
// [다익스트라 변형] 최소시간으로 우큐를 진행하면서 비용이 M을 넘지않도록 함
#include <vector>
#include <queue>
using namespace std;

#define MAXN 103

struct Edge {
	int to, time, cost;
	bool operator<(const Edge& r)const {
		return time > r.time;
	}
};

vector<Edge> adj[MAXN];
void add(int sCity, int eCity, int mCost, int mTime) {
	adj[sCity].push_back({ eCity, mTime, mCost });
}

void init(int N, int K, int sCity[], int eCity[], int mCost[], int mTime[]) {
	for (int i = 0; i < N; i++) adj[i].clear();
	for (int i = 0; i < K; i++) add(sCity[i], eCity[i], mCost[i], mTime[i]);
}

int cost(int M, int sCity, int eCity) {
	priority_queue<Edge> pq;
	pq.push({ sCity, 0, 0 });
	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.cost > M) continue;
		if (cur.to == eCity) return cur.time;
		for (Edge nx : adj[cur.to]) 
			pq.push({ nx.to, cur.time + nx.time, cur.cost + nx.cost});
	}
	return -1;
}
#endif // 1
```
