```cpp
#if 1
#include<string>
#include <vector>
#include <unordered_map>
#include <map>
#include <queue>
using namespace std;

unordered_map<string, int> idMap;
#define MAXL 11 
#define MAXN 50003
vector<int> adj[MAXN];
int p[MAXN], depth[MAXN];

int getID(char c[]) {
	return idMap.count(c) ? idMap[c] : idMap[c] = idMap.size();
}
void init(char mRootSpecies[MAXL]){
	idMap.clear(), getID(mRootSpecies);
	for (int i = 0; i < MAXN; i++) adj[i].clear();
}

void add(char mSpecies[MAXL], char mParentSpecies[MAXL]){
	int cid = getID(mSpecies), pid = getID(mParentSpecies);
	p[cid] = pid;
	depth[cid] = depth[pid] + 1;
	adj[pid].push_back(cid), adj[cid].push_back(pid);
}

int getDistance(char mSpecies1[MAXL], char mSpecies2[MAXL]){
	int u = getID(mSpecies1), v = getID(mSpecies2);
	if (depth[u] < depth[v]) swap(u, v);
	int dist = 0;
	while (depth[u] != depth[v]) u = p[u], dist++;
	if (u == v) return dist;
	while (u != v) u = p[u], v = p[v], dist += 2;
	return dist;
}

int dfs(int cur, int p, int dist) { 
	if (dist == 0) return 1; // 더 이상 쪼개지지 않는 케이스: 기저 사례(Base case)
	int ret = 0;
	for (auto nx : adj[cur]) {
		if (nx == p) continue; // 탐색했었던 부모를 다시 탐색 x
		ret += dfs(nx, cur, dist - 1); // 더 탐색한 결과를 더해서 리턴
	}
	return ret;
}

struct Data{
	int to, d, prev;
};
int bfs(int s, int dist) {
	queue<Data> q;
	q.push({ s,0, -1 });
	int ret = 0;
	while (!q.empty()) {
		auto cur = q.front(); q.pop();
		for (int nx : adj[cur.to]) {
			if (nx == cur.prev) continue;
			if (cur.d == dist - 1) {
				ret++; continue;
			}
			q.push({ nx,cur.d + 1, cur.to });
		}
	}
	return ret;
}

int getCount(char mSpecies[MAXL], int mDistance){ // mSpecies에서 거리가 mDistance인 종의 개수를 세고 그 값을 반환한다.
	int s = getID(mSpecies);
	return dfs(s, -1, mDistance); // 250 ms
	//return bfs(s, mDistance); // 430 ms
}
#endif // 1
```
