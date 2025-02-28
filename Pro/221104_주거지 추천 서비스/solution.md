```cpp
#if 1
// 3개의 중심도시를 정렬해서 hash로 만들었음
// 출근거리는 bfs로 계산하고 3개의 중심도시로 만들어지는 7가지의 거리는 한계거리안에서만 추가함
// 도시는 전부 List배열에 넣고 계산
// 두 도시를 지나는 노선이 중복으로 주어지는 경우가 있기때문에 기존대비 낮은 값을때만 추가함
// 새로운 노선추가시 주택가격, 출근거리, id중 주택가격은 놔두고 update해야 함
// 추천도시는 우큐에 넣고 update되어 있지 않은 정보는 빼고 추천함
#include <vector>
#include <unordered_map>
#include <queue>
#include <string.h>
#include <algorithm>
using namespace std;

#define MAXN 3003

struct Edge {
	int to, cost;
	bool operator<(const Edge& r)const {
		return cost > r.cost;
	}
};
vector<Edge> adj[MAXN];
int houseCost[MAXN], dist[7][MAXN], center[3];

struct Info {
	int c, d, id;
	bool operator<(const Info& r)const {
		if (c != r.c) return c > r.c;
		if (d != r.d) return d > r.d;
		return id > r.id;
	}
};
priority_queue<Info> pq[7];

int N;
unordered_map<int, int> m;
vector<int> List;

int getHash(int a, int b) {
	return a * 10000 + b;
}

void init(int N, int mDownTown[]) {
	::N = N, m.clear(), List.clear();
	for (int i = 0; i < 7; i++) pq[i] = {};
	for (int i = 1; i <= N; i++) houseCost[i] = 0, adj[i].clear();
	for (int i = 0; i < 3; i++) center[i] = mDownTown[i];
	sort(center, center + 3);
	for (int i = 0; i < 3; i++) {
		m[center[i]] = i;
		for (int j = 0; j < 3; j++)
			if (i == j || i > j) continue;
			else m[getHash(center[i], center[j])] = i + j + 2;
	}
}

void newLine(int M, int mCityIDs[], int mDistances[]) {
	for (int i = 0; i < 7; i++) pq[i] = {};
	bool flag;
	for (int i = 0; i < M - 1; i++) {
		flag = true;
		for (int j = 0; j < adj[mCityIDs[i]].size(); j++)
			if (adj[mCityIDs[i]][j].to == mCityIDs[i + 1] &&
				adj[mCityIDs[i]][j].cost > mDistances[i]) {
				adj[mCityIDs[i]][j].cost = mDistances[i];
				flag = false;
				break;
			}
		if (!flag) {
			for (int j = 0; j < adj[mCityIDs[i + 1]].size(); j++)
				if (adj[mCityIDs[i + 1]][j].to == mCityIDs[i]) {
					adj[mCityIDs[i + 1]][j].cost = mDistances[i];
					break;
				}
		}
		if (flag) {
			adj[mCityIDs[i]].push_back({ mCityIDs[i + 1], mDistances[i] });
			adj[mCityIDs[i + 1]].push_back({ mCityIDs[i], mDistances[i] });
		}
		List.push_back(mCityIDs[i]);
	}
	List.push_back(mCityIDs[M - 1]);
}

int cost[MAXN];
void bfs(int s) {
	for (int i = 1; i <= N; i++) cost[i] = INT_MAX;
	bool v[MAXN]; memset(v, 0, sizeof(v));
	priority_queue<Edge> pq;
	cost[s] = 0;
	pq.push({ s, 0 });
	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.cost > cost[cur.to]) continue;
		v[cur.to] = true;
		for (Edge nx : adj[cur.to]) {
			if (v[nx.to]) continue;
			int nextCost = cur.cost + nx.cost;
			if (cost[nx.to] > nextCost)
				pq.push({ nx.to, cost[nx.to] = nextCost });
		}
	}
}

int limit;
void changeLimitDistance(int mLimitDistance) {
	limit = mLimitDistance;
	for (int i = 0; i < 3; i++) {
		bfs(center[i]);
		for (int nx : List) {
			dist[i][nx] = cost[nx];
			if (dist[i][nx] <= limit && nx != center[0] && nx != center[1] && nx != center[2])
				pq[i].push({ houseCost[nx], dist[i][nx], nx });
		}
	}
	for (int nx : List) {
		dist[3][nx] = dist[0][nx] + dist[1][nx];
		if (dist[3][nx] <= limit && nx != center[0] && nx != center[1] && nx != center[2])
			pq[3].push({ houseCost[nx], dist[3][nx], nx });

		dist[4][nx] = dist[0][nx] + dist[2][nx];
		if (dist[4][nx] <= limit && nx != center[0] && nx != center[1] && nx != center[2])
			pq[4].push({ houseCost[nx], dist[4][nx], nx });

		dist[5][nx] = dist[1][nx] + dist[2][nx];
		if (dist[5][nx] <= limit && nx != center[0] && nx != center[1] && nx != center[2])
			pq[5].push({ houseCost[nx], dist[5][nx], nx });

		dist[6][nx] = dist[0][nx] + dist[1][nx] + dist[2][nx];
		if (dist[6][nx] <= limit && nx != center[0] && nx != center[1] && nx != center[2])
			pq[6].push({ houseCost[nx], dist[6][nx], nx });
	}
}

int findCity(int mOpt, int mDestinations[]) {
	int id = 0;
	if (mOpt == 1) id = m[mDestinations[0]];
	else if (mOpt == 2) {
		if (mDestinations[0] > mDestinations[1]) swap(mDestinations[0], mDestinations[1]);
		id = m[getHash(mDestinations[0], mDestinations[1])];
	}
	else id = 6; // mDownTown이 3개일때는 id가 6
	if (pq[id].empty()) return -1;
	while (true) {
		int nid = pq[id].top().id, c = pq[id].top().c;
		if (c != houseCost[nid]) pq[id].pop();
		else break;
	}
	int nid = pq[id].top().id;
	houseCost[nid]++;
	for (int i = 0; i < 7; i++) {
		if (dist[i][nid] > limit) continue;
		pq[i].push({ houseCost[nid], dist[i][nid], nid });
	}
	return nid;
}
#endif // 1

```
