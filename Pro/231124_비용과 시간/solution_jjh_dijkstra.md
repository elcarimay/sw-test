```cpp
#include <vector>
#include <queue>
using namespace std;

struct Edge{
	int to, cost, time;
};

struct cmp{
	bool operator()(const Edge & A, const Edge & B) const {
		return A.time > B.time;
	}
};

const int MAX = 101;
int N;
vector<Edge> adj[MAX];

void add(int sCity, int eCity, int mCost, int mTime) {
	adj[sCity].push_back({ eCity, mCost, mTime });
}

void init(int N, int K, int sCity[], int eCity[], int mCost[], int mTime[]) {
	::N = N;
	for (int i = 0; i <= N; i++)
		adj[i].clear();
	for (int i = 0; i < K; i++)
		add(sCity[i], eCity[i], mCost[i], mTime[i]);
}

const int INF = 987654321;
int time[10'001][101];

int cost(int M, int sCity, int eCity) {
	for (int i = 0; i <= M; i++)
		for (int j = 0; j < N; j++)
			time[i][j] = INF;

	priority_queue<Edge, vector<Edge>, cmp> Q;
	Q.push({ sCity, M, 0 });
	time[M][sCity] = 0;

	int ans = INF;
	while (Q.size())
	{
		auto cur = Q.top();		Q.pop();
		if (cur.time > time[cur.cost][cur.to])
			continue;
		if (cur.to == eCity)
			return cur.time;
		for (auto & next : adj[cur.to])
		{
			// 비용이 더 크면 갈 수 없다.
			if (next.cost > cur.cost)
				continue;
			int nextTime = time[cur.cost][cur.to] + next.time;
			if (nextTime < time[cur.cost - next.cost][next.to]) {
				time[cur.cost - next.cost][next.to] = nextTime;
				Q.push({ next.to, cur.cost - next.cost, nextTime });
			}
		}
	}
	return ans == INF ? -1 : ans;
}
```
