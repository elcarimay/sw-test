```cpp
#if 1 // 137 ms
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;

struct Edge {
	int to, time, power, id;
};

#define INF 987654321
#define MAXN 503

int N, idCnt, dist[MAXN], h[MAXN], chargeRate[MAXN];
vector<Edge> adj[MAXN], radj[MAXN];
unordered_map<long long, pair<int, int>> idPos; // mid -> (from, idx)

void InfectTimePQ(int m, int city[], int start[]) {
	for (int i = 0; i < N; i++) dist[i] = INF;
	priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq; // time, city 오름차순
	for (int i = 0; i < m; i++) {
		int to = city[i], time = start[i];
		if (dist[to] > time) pq.push(make_pair(dist[to] = time, to));
	}
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		int time = cur.first, u = cur.second;
		if (time != dist[u]) continue;
		for (auto nx : adj[u]) {
			int v = nx.to, nt = time + nx.time;
			if (dist[v] > nt) pq.push(make_pair(dist[v] = nt, v));
		}
	}
}

void reversePQ(int target) {
	for (int i = 0; i < N; i++) h[i] = INF;
	priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
	h[target] = 0;
	pq.push(make_pair(0, target));
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		int time = cur.first, u = cur.second;
		if (time != h[u]) continue;
		for (auto nx : radj[u]) {
			int v = nx.to, nt = time + nx.time;
			if (nt < h[v]) pq.push(make_pair(h[v] = nt, v));
		}
	}
}

// ===================== EV 최단시간: A* + 1시간충전 =====================
int minTime(int B, int s, int e) { // B: 최대충전용량
	if (dist[s] <= 0) return -1;
	int W = B + 1, g[MAXN*301]; // g = 실제 시간, f = g + h[u], 배터리잔량이 (0~B)까지 B+1이므로 W는 B + 1임
	for (int i = 0; i < N * W; i++) g[i] = INF;
	struct Node { // f: g+h[u] 현재까지 걸린시간 + 앞으로 남은 최소 예상시간(h[u]), st: u도시번호 b현재배터리잔량 u * (B+1) + b 도시,배터리를 쌍으로 표현가능
		int f, g, st; // f: 우선순위값, g: 현재상태까지 도달하는데 걸린 최소시간(출발에서 (도시2,배터리5)까지 17시간 걸렸다면 g=17 ), 
		bool operator<(const Node& r)const {
			if (f != r.f) return f > r.f;
			return g > r.g;
		}
	};
	priority_queue<Node> pq;
	int s0 = s * W + B; // 출발도시에서 배터리 완충상태
	pq.push(Node{ h[s], g[s0] = 0, s0 });
	while (!pq.empty()) {
		Node nd = pq.top(); pq.pop();
		int t = nd.g, st = nd.st;
		if (t != g[st]) continue;
		int u = st / W, b = st % W;
		if (t >= dist[u]) continue;
		if (u == e) return t; // A*: 최초 팝이 최적. t < dist[e]는 이미 safeStay(u,t)에서 u==e이면 확인됨
		if (b < B) { // ---- 1) 1시간 충전 전이 ----
			int nb = b + chargeRate[u];
			if (nb > B) nb = B;
			int t2 = t + 1;
			if (t2 < dist[u]) {
				int st2 = u * W + nb;
				if (g[st2] > t2) pq.push(Node{ t2 + h[u], g[st2] = t2, st2 }); // 휴리스틱은 '도시' 기준
			}
		}
		for (auto nx : adj[u]) { // ---- 2) 도로 주행 전이 ----
			if (b < nx.power) continue;
			int time = t + nx.time;
			if (time < dist[nx.to]) {
				int st2 = nx.to * W + b - nx.power;
				if (g[st2] > time) pq.push(Node{ time + h[nx.to], g[st2] = time, st2 }); // 목적지까지 도로시간 휴리스틱
			}
		}
	}
	return -1;
}

void init(int N, int mCharge[], int K, int mId[], int sCity[], int eCity[], int mTime[], int mPower[]){
	::N = N, idPos.clear();
	for (int i = 0; i < N; ++i) chargeRate[i] = mCharge[i], adj[i].clear(), radj[i].clear();
	for (int i = 0; i < K; ++i) {
		idPos[mId[i]] = make_pair(sCity[i], (int)adj[sCity[i]].size());
		adj[sCity[i]].push_back(Edge{ eCity[i], mTime[i], mPower[i], mId[i] });
		radj[eCity[i]].push_back(Edge{ sCity[i], mTime[i], 0, mId[i]}); // 역그래프(휴리스틱 계산용): 간선 방향 반대로 저장, 전력은 사용 안 함
	}
}

void add(int mId, int s, int e, int t, int p){
	idPos[mId] = make_pair(s, (int)adj[s].size());
	adj[s].push_back(Edge{e, t, p, mId});
	radj[e].push_back(Edge{s, t, 0, mId});
}

void remove(int mId){
	auto it = idPos.find(mId);
	int u = it->second.first, pos = it->second.second, last = (int)adj[u].size() - 1;
	if (pos != last) {
		swap(adj[u][pos], adj[u][last]);
		idPos[adj[u][pos].id] = make_pair(u, pos);
	}
	Edge rem = adj[u].back(); adj[u].pop_back();
	idPos.erase(it);
	auto& R = radj[rem.to]; // 역그래프에서도 제거: 선형 탐색(간선 수 제한 K<=4000이므로 충분히 빠름)
	for (int i = 0; i < R.size();i++) {
		if (R[i].id == rem.id && R[i].to == u && R[i].time == rem.time) {
			int last2 = (int)R.size() - 1;
			if (i != last2) swap(R[i], R[last2]);
			R.pop_back();
			break;
		}
	}
}

int cost(int B, int sCity, int eCity, int M, int mCity[], int mTime[]){
	InfectTimePQ(M, mCity, mTime); // 1) 감염 도착 시각
	if (dist[sCity] <= 0) return -1; // 출발 즉시 감염
	reversePQ(eCity); // 2) 휴리스틱 h[u]: 목적지까지 "도로시간만" 최단거리 (감염/충전 무시 → 낙관적)
	int ans = minTime(B, sCity, eCity); // 3) EV 최단 시간 (A* 알고리즘)
	return ans;
}
#endif
```
