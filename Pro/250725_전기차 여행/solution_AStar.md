```cpp
#if 1 // 133 ms
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;

struct Edge {
	int to, time, power, id;
};

#define INF 987654321
#define MAXN 503

int N, dist[MAXN], h[MAXN], chargeRate[MAXN];
vector<Edge> adj[MAXN], radj[MAXN];
unordered_map<int, pair<int, int>> hmap; // mid -> city index
priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq; // time, city 오름차순

void dijkstra(int a[], vector<Edge>(&vec)[MAXN]) {
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		int time = cur.first, u = cur.second;
		if (time != a[u]) continue;
		for (auto nx : vec[u]) {
			int v = nx.to, nt = time + nx.time;
			if (a[v] > nt) pq.push(make_pair(a[v] = nt, v));
		}
	}
}

void InfectTimePQ(int m, int city[], int start[]) {
	for (int i = 0; i < N; i++) dist[i] = INF;
	while (!pq.empty()) pq.pop();
	for (int i = 0; i < m; i++) if (dist[city[i]] > start[i]) pq.push(make_pair(dist[city[i]] = start[i], city[i]));
	dijkstra(dist, adj);
}

void reversePQ(int target) {
	for (int i = 0; i < N; i++) h[i] = INF;
	while (!pq.empty()) pq.pop();
	h[target] = 0;
	pq.push(make_pair(0, target));
	dijkstra(h, radj);
}

int minTime(int B, int s, int e) { // ===================== EV 최단시간: A* + 1시간충전 ===================== B: 최대충전용량
	if (dist[s] <= 0) return -1;
	int g[MAXN][303]; // g = 실제 시간, f = g + h[u]
	for (int i = 0; i < N; i++) for (int j = 0; j <= B; j++) g[i][j] = INF; //vector<vector<int>> g(MAXN, vector<int>(303, INF));
	struct Node { // f: g + h[u] -> 현재까지 걸린시간 + 앞으로 남은 최소 예상시간(h[u])
		int f, g, u, b; // f: 우선순위값, g: 현재상태까지 도달하는데 걸린 최소시간
		bool operator<(const Node& r)const {
			if (f != r.f) return f > r.f;
			return g > r.g;
		}
	};
	priority_queue<Node> pq;
	pq.push(Node{ h[s], g[s][B] = 0, s, B});
	while (!pq.empty()) {
		Node cur = pq.top(); pq.pop();
		int t = cur.g, u = cur.u, b = cur.b;
		if ((t != g[u][b]) || (t >= dist[u])) continue;
		if (u == e) return t; // A* algorithm: 최초 팝이 최적.
		if (b < B) { // ---- 1) 1시간 충전 전이 ----
			int nb = (b + chargeRate[u] > B) ? B : b + chargeRate[u], t2 = t + 1;
			if (t2 < dist[u] && g[u][nb] > t2) pq.push(Node{ t2 + h[u], g[u][nb] = t2, u, nb }); // 휴리스틱은 '도시' 기준
		}
		for (auto nx : adj[u]) { // ---- 2) 도로 주행 전이 ----
			if (b < nx.power) continue;
			int time = t + nx.time;
			if (time < dist[nx.to]) {
				int bat = b - nx.power;
				if (g[nx.to][bat] > time) pq.push(Node{time + h[nx.to], g[nx.to][bat] = time, nx.to, bat}); // 목적지까지 도로시간 휴리스틱
			}
		}
	}
	return -1;
}

void add(int mId, int sCity, int eCity, int mTime, int mPower) {
	hmap[mId] = make_pair(sCity, (int)adj[sCity].size());
	adj[sCity].push_back(Edge{ eCity, mTime, mPower, mId });
	radj[eCity].push_back(Edge{ sCity, mTime, 0, mId }); // 역그래프(휴리스틱 계산용): 간선 방향 반대로 저장, 전력은 사용 안 함
}

void init(int N, int mCharge[], int K, int mId[], int sCity[], int eCity[], int mTime[], int mPower[]) {
	::N = N;
	for (int i = 0; i < N; ++i) chargeRate[i] = mCharge[i], adj[i].clear(), radj[i].clear();
	for (int i = 0; i < K; ++i) add(mId[i], sCity[i], eCity[i], mTime[i], mPower[i]);
}

void remove(int mId) {
	int u = hmap[mId].first, idx = hmap[mId].second;
	hmap.erase(mId);
	int last = (int)adj[u].size() - 1;
	if (idx != last) {
		swap(adj[u][idx], adj[u][last]);
		hmap[adj[u][idx].id] = make_pair(u, idx);
	}
	Edge tmp = adj[u].back(); adj[u].pop_back();
	auto& R = radj[tmp.to]; // 역그래프에서도 제거: 선형 탐색(간선 수 제한 K<=4000이므로 충분히 빠름)
	for (int i = 0; i < R.size(); i++) if (R[i].id == tmp.id && R[i].to == u && R[i].time == tmp.time) {
		int last2 = (int)R.size() - 1;
		if (i != last2) swap(R[i], R[last2]);
		R.pop_back();
		break;
	}
}

int cost(int B, int sCity, int eCity, int M, int mCity[], int mTime[]) {
	InfectTimePQ(M, mCity, mTime); // 1) 감염 도착 시각
	if (dist[sCity] <= 0) return -1; // 출발 즉시 감염
	reversePQ(eCity); // 2) 휴리스틱 h[u]: 목적지까지 "도로시간만" 최단거리 (감염/충전 무시 → 낙관적)
	return minTime(B, sCity, eCity); // 3) EV 최단 시간 (A* 알고리즘)
}
#endif
```
