```cpp
#include <vector>
#include <unordered_map>
using namespace std;

#define MAXN 10003

struct Edge {
	int to, cost;
};
vector<Edge> adj[MAXN];
unordered_map<int, int> idMap;
int p[MAXN], depth[MAXN], cost[MAXN];


void init(int mDevice){
	idMap.clear(), idMap[mDevice] = idMap.size();
	for (int i = 0; i < MAXN; i++) adj[i].clear();
	depth[0] = 0;
}

void connect(int mOldDevice, int mNewDevice, int mLatency){
	int oldid = idMap[mOldDevice], newid = idMap[mNewDevice] = idMap.size();
	adj[oldid].push_back({ newid, mLatency });
	adj[newid].push_back({ oldid, mLatency });
	p[newid] = oldid, depth[newid] = depth[oldid] + 1;
	cost[newid] = mLatency;
	return;
}

int measure(int mDevice1, int mDevice2){ // LCA 이용
	int ret = 0;
	int id1 = idMap[mDevice1], id2 = idMap[mDevice2];
	if (depth[id1] < depth[id2]) swap(id1, id2);
	while (depth[id1] > depth[id2]) {
		ret += cost[id1], id1 = p[id1];
	}
	if (id1 == id2) return ret;
	while (p[id1] != p[id2]) {
		ret += cost[id1], ret += cost[id2];
		id1 = p[id1], id2 = p[id2];
	}
	return ret + cost[id1] + cost[id2];
}

int dfs(int cur, int p) {
	int ret = 0;
	for (auto& nx : adj[cur]) {
		if (nx.to == p) continue;
		ret = max(ret, dfs(nx.to, cur) + nx.cost);
	}
	return ret;
}

// 자식이 한개인 경우는 본인이 맨끝이 되고, 아닌 경우는 가장 깊이가 큰 2개를 선택한다.
int test(int mDevice){
	int id = idMap[mDevice];
	if (adj[id].size() == 1)
		return dfs(id, -1);
	int maxVal[2] = { 0,0 };
	for (auto& nx : adj[id]) {
		int r = dfs(nx.to, id) + nx.cost;
		if (maxVal[0] < r) {
			maxVal[1] = maxVal[0];
			maxVal[0] = r;
		}
		else if (maxVal[1] < r) maxVal[1] = r;
	}
	return maxVal[0] + maxVal[1];
}
```
