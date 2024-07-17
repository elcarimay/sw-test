```cpp
#include <vector>
#include <algorithm>
#include <unordered_map>
using namespace std;

#define INF 987654321

struct Train
{
	int sid, eid, inter;
}tr[205];

unordered_map<int, int> trMap; // mid, id

struct Edge
{
	int to, cost;
};

vector<int> st[100005]; // station
vector<Edge> adj[205];
int N, K, trCnt;
void init(int N, int K, int mId[], int sId[], int eId[], int mInterval[]) {
	::N = N, ::K = K, trCnt = 0;
	for (int i = 1; i < N; i++) st[i].clear();
	for (int i = 0; i < 200; i++) adj[i].clear();
	bool flag;
	for (int i = 0; i < K; i++) {
		tr[trCnt] = { sId[i], eId[i], mInterval[i] };
		trMap[mId[i]] = trCnt++;
		flag = 0;
		for (int j = sId[i]; j <= eId[i]; j = j + mInterval[i]) {
			if (!st[j].empty()) {
				for (int nx : st[j]) {
					if (flag) break;
					adj[nx].push_back({ i,1 });
					adj[i].push_back({ nx,1 });
					break;
				}
				flag = 1;
			}
			st[j].push_back(i);
		}
	}
}

void add(int mId, int sId, int eId, int mInterval) {
	bool flag;
	tr[trCnt] = { sId, eId, mInterval };
	trMap[mId] = trCnt;
	flag = 0;
	for (int j = sId; j <= eId; j = j + mInterval) {
		if (!st[j].empty()) {
			for (int nx : st[j]) {
				if (flag) break;
				adj[nx].push_back({ trCnt,1 });
				adj[trCnt].push_back({ nx,1 });
				break;
			}
			flag = 1;
		}
		st[j].push_back(trCnt);
	}
	trCnt++;
}

void remove(int mId) {
	int id = trMap[mId];
	for (Edge nx : adj[id])
		for (int i = 0; i < adj[nx.to].size(); i++)
			if (adj[nx.to][i].to == id) adj[nx.to].erase(adj[nx.to].begin() + i);
	adj[id].clear();
	for (int j = tr[id].sid; j <= tr[id].eid; j = j + tr[id].inter)
		for (int i = 0; i < st[j].size(); i++)
			if (st[j][i] == id) st[j].erase(st[j].begin() + i);
	tr[id] = {};
	trMap.erase(mId);
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
	for (int nx : st[sId]) {
		bfs(nx);
		for (int nx2 : st[eId]) ret = min(ret, cost[nx2]);
	}
	return ret == INF ? -1 : ret;
}
```
