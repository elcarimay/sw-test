```cpp
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <set>
using namespace std;

#define INF 987654321

struct Tra {
	int s, e, i;
	bool state;
}trr[205];

unordered_map<int, int> trMap; // mid, id
unordered_map<int, int> stMap; // station number, id
int stCnt;

int get_stid(int num) {
	if (stMap.find(num) == stMap.end()) return stCnt++;
	else return stMap.find(num)->second;
}

struct Edge
{
	int to, cost;
};

vector<Edge> adj[205]; bool flag;
int N, K, trCnt;
set<int> st[100'005];

void init(int N, int K, int mId[], int sId[], int eId[], int mInterval[]) {
	::N = N, ::K = K, trCnt = 0, trMap.clear(), stMap.clear(), stCnt = 0;
	for (int i = 0; i < 200; i++) adj[i].clear();
	for (int i = 0; i < N; i++) st[i].clear();
	for (int i = 0; i < K; i++) {
		trr[trCnt] = { sId[i], eId[i], mInterval[i], 1 };
		trMap[mId[i]] = trCnt;
		for (int j = 0; j < trCnt; j++) {
			flag = 0;
			for (int ii = trr[j].s; ii <= trr[j].e; ii = ii + trr[j].i) {
				int id = get_stid(ii);
				stMap[ii] = id, st[id].insert(j);
				for (int jj = trr[trCnt].s; jj <= trr[trCnt].e; jj = jj + trr[trCnt].i) {
					if (ii == jj) { flag = 1; break; }
				}
				if (flag) break;
			}
			if (flag) {
				adj[j].push_back({ trCnt,1 });
				adj[trCnt].push_back({ j,1 });
			}
		}
		for (int ii = trr[trCnt].s; ii <= trr[trCnt].e; ii = ii + trr[trCnt].i) {
			int id = get_stid(ii);
			stMap[ii] = id, st[id].insert(trCnt);
		}
		trCnt++;
	}
}

void add(int mId, int sId, int eId, int mInterval) {
	trr[trCnt] = { sId, eId, mInterval, 1};
	trMap[mId] = trCnt;
	for (int j = 0; j < trCnt; j++) {
		flag = 0;
		for (int ii = trr[j].s; ii <= trr[j].e; ii = ii + trr[j].i) {
			if (!trr[j].state) continue;
			int id = get_stid(ii);
			stMap[ii] = id, st[id].insert(j);
			for (int jj = trr[trCnt].s; jj <= trr[trCnt].e; jj = jj + trr[trCnt].i) {
				if (ii == jj) { flag = 1; break; }
			}
			if (flag) break;
		}
		if (flag) {
			adj[j].push_back({ trCnt,1 });
			adj[trCnt].push_back({ j,1 });
		}
	}
	for (int ii = trr[trCnt].s; ii <= trr[trCnt].e; ii = ii + trr[trCnt].i) {
		int id = get_stid(ii);
		stMap[ii] = id, st[id].insert(trCnt);
	}
	trCnt++;
}

void remove(int mId) {
	int id = trMap[mId];
	for (Edge nx : adj[id])
		for (int i = 0; i < adj[nx.to].size(); i++)
			if (adj[nx.to][i].to == id) adj[nx.to].erase(adj[nx.to].begin() + i);
	adj[id].clear();
	trMap.erase(mId);
	for (int i = trr[id].s; i <= trr[id].e; i = i + trr[id].i) {
		int id2 = stMap[i];
		if (st[id2].find(id) != st[id2].end())
			st[id2].erase(id);
	}
	trr[id].state = 0;
}

Edge que[205];
int head, tail, cost[205];
void bfs(int s) {
	head = tail = 0;
	for (int i = 0; i < trCnt; i++) cost[i] = INF;
	cost[s] = 0;
	que[tail++] = { s,0 };
	while (head < tail) {
		Edge cur = que[head++];
		if (cur.cost > cost[cur.to]) continue;
		for (Edge nx : adj[cur.to]) {
			int nextCost = nx.cost + cur.cost;
			if (cost[nx.to] > nextCost) {
				cost[nx.to] = nextCost;
				que[tail++] = { nx.to, cost[nx.to] };
			}
		}
	}
}

int calculate(int sId, int eId) {
	int ret = INF;
	if (stMap.find(sId) == stMap.end())	return -1;
	if (stMap.find(eId) == stMap.end())	return -1;
	int s = stMap[sId], e = stMap[eId];
	for (int nx : st[s]) {
		bfs(nx);
		for (int nx2 : st[e])
			ret = min(ret, cost[nx2]);
	}
	return ret == INF ? -1 : ret;
}
```
