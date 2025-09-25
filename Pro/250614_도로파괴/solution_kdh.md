```cpp
#if 1 // 170 ms
// 최단시간경로를 저장후 경로를 하나씩 지우면서 최단거리를 구하면서 최대시간을 구함
#include <unordered_map>
#include <vector>
#include <queue>
using namespace std;

#define MAXN 1003
#define INF 987654321

pair<int, int>info[6503]; // scity, idx
unordered_map <int, int> hmap; // id
int idCnt, N, K;
struct Edge {
	int to, time, id;
	bool operator<(const Edge& r)const {
		return time > r.time;
	}
};
vector<Edge> adj[MAXN];

void add(int mId, int sCity, int eCity, int mTime) {
	int id = hmap[mId] = idCnt++;
	info[id] = { sCity, (int)adj[sCity].size() };
	adj[sCity].push_back({ eCity, mTime, id });
}

void init(int N, int K, int mId[], int sCity[], int eCity[], int mTime[]) {
	::N = N, ::K = K, idCnt = 0, hmap.clear();
	for (int i = 0; i < N; i++) adj[i].clear();
	for (int i = 0; i < K; i++) add(mId[i], sCity[i], eCity[i], mTime[i]);
}

void remove(int mId) {
	int s = info[hmap[mId]].first, idx = info[hmap[mId]].second;
	int last = (int)adj[s].size() - 1;
	if (idx != last) {
		info[adj[s][last].id].second = idx;
		swap(adj[s][last], adj[s][idx]);
	}
	adj[s].pop_back();
	hmap.erase(mId);
}

int cost[MAXN];
vector<pair<int, int>> path;
int dijkstra(int s, int e) {
	path.clear();
	for (int i = 0; i < N; i++) cost[i] = INF;
	vector<pair<int, int>> parent(N, make_pair(-1, -1));
	priority_queue<Edge> pq;
	pq.push({ s,cost[s] = 0 });
	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.time > cost[cur.to]) continue;
		for (Edge nx : adj[cur.to]) {
			int nextTime = cur.time + nx.time;
			if (cost[nx.to] > nextTime) parent[nx.to] = make_pair(cur.to, nx.id), pq.push({ nx.to, cost[nx.to] = nextTime });
		}
	}
	if (cost[e] == INF) return -1;
	for (int v = e; v != -1; v = parent[v].first) path.push_back(make_pair(v, parent[v].second));
	reverse(path.begin(), path.end());
	return cost[e];
}

int dijkstra2(int s, int e, int id) {
	for (int i = 0; i < N; i++) cost[i] = INF;
	priority_queue<Edge> pq;
	pq.push({ s,cost[s] = 0 });
	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.time > cost[cur.to]) continue;
		if (cur.to == e) return cost[e];
		for (Edge nx : adj[cur.to]) {
			if (nx.id == id) continue;
			int nextTime = cur.time + nx.time;
			if (cost[nx.to] > nextTime) pq.push({ nx.to, cost[nx.to] = nextTime });
		}
	}
	return -1;
}

int calculate(int sCity, int eCity) {
	int ret = 1, minTime = dijkstra(sCity, eCity), tmpTime = 0;
	if (minTime == -1) return -1;
	for (int i = 1; i < (int)path.size(); i++) {
		tmpTime = dijkstra2(sCity, eCity, path[i].second);
		if (tmpTime == -1) return -1;
		if (ret < tmpTime) ret = tmpTime;
	}
	return ret == -1 ? -1 : ret - minTime;
}
#endif // 1
```
