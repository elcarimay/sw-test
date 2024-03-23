```cpp
// 단방향이므로 한번만 지나가면 됨.
// 비용이 높은 경로로 지나가면서 가장 낮은 비용을 반환하면 됨.
#include <vector>
#include <queue>
using namespace std;

#define INF 987654321
#define MAX_N 1000
#define MAX 4000

struct Edge
{
	int to, mLimit;
	bool operator<(const Edge& edge)const {
		return mLimit < edge.mLimit;
	}
};

int n;
vector<Edge> adj[MAX];
void init(int N, int K, int sCity[], int eCity[], int mLimit[]) {
	n = N;
	for (int i = 0; i < n; i++) adj[i].clear();
	for (int i = 0; i < K; i++)
		adj[sCity[i]].push_back({ eCity[i],mLimit[i] });
}

void add(int sCity, int eCity, int mLimit) {
	adj[sCity].push_back({ eCity,mLimit });
}

int calculate(int sCity, int eCity) {
	int visited[MAX_N];
	fill(visited, visited + n, -1);
	int min_v = INF;
	priority_queue<Edge> Q;
	Q.push({ sCity,0 });
	while (!Q.empty()) {
		auto cur = Q.top(); Q.pop();
		if (min_v > cur.mLimit && cur.to != sCity) min_v = cur.mLimit;
		visited[cur.to] = 1;
		if (cur.to == eCity) return min_v;
		for (auto nx : adj[cur.to]) {
			if (visited[nx.to] != 1) {
				Q.push(nx);
			}
		}
	}
	return -1;
}
```
