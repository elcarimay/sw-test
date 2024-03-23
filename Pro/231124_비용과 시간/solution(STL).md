```cpp
// 다익스트라로 모든 비용을 업데이트 하는데 비용을 계속 더해서 Q에 남게함.
// 남아있는 Q들 중에서 M값내에 있는 Q만 선별해서 반환함.
#include <vector>
#include <queue>
using namespace std;

#define MAX_N 100

struct Edge
{
	int to, mCost, mTime;
	bool operator<(const Edge& edge)const {
		return mTime > edge.mTime;
	}
};

vector<Edge> adj[MAX_N];
int n;
void init(int N, int K, int sCity[], int eCity[], int mCost[], int mTime[]) {
	n = N;
	for (int i = 0; i < N; i++) adj[i].clear();
	for (int i = 0; i < K; i++)
		adj[sCity[i]].push_back({ eCity[i],mCost[i], mTime[i] });
}

void add(int sCity, int eCity, int mCost, int mTime) {
	adj[sCity].push_back({ eCity,mCost, mTime });
}

int cost(int M, int sCity, int eCity) {
	priority_queue<Edge> Q;
	Q.push({ sCity, 0, 0 });
	while (!Q.empty()) {
		auto cur = Q.top(); Q.pop();
		if (M - cur.mCost < 0) continue;
		if (cur.to == eCity) return cur.mTime;
		for (auto nx : adj[cur.to]) {
			Q.push({ nx.to, nx.mCost + cur.mCost, nx.mTime + cur.mTime});
		}
	}
	return -1;
}
```
