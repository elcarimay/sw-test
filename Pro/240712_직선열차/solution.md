```cpp
#include <vector>
#include <algorithm>
#include <unordered_map>
using namespace std;

#define INF 987654321

struct Tra {
	int s, e, i;
	bool state;
}trr[205];

unordered_map<int, int> trMap; // mid, id
vector<int> st[100001];
vector<int> adj[205]; bool flag;
int N, K, trCnt;

void add(int mId, int sId, int eId, int mInterval);

void init(int N, int K, int mId[], int sId[], int eId[], int mInterval[]) {
	::N = N, ::K = K, trCnt = 0, trMap.clear();
	for (int i = 0; i < 200; i++) adj[i].clear();
	for (int i = 0; i < N; i++) st[i].clear();
	for (int i = 0; i < K; i++)	add(mId[i], sId[i], eId[i], mInterval[i]);
}

void add(int mId, int sId, int eId, int mInterval) {
	trr[trCnt] = { sId, eId, mInterval, 1 };
	trMap[mId] = trCnt;

	for (int i = sId; i <= eId; i += mInterval) {
		for (int old : st[i]) {
			if (!trr[old].state) continue;
			if (eId < trr[old].s) continue;
			if (sId > trr[old].e) continue;
			if ((i - trr[old].s) % trr[old].i == 0){
				adj[old].push_back({ trCnt });
				adj[trCnt].push_back({ old });
			}
		}
		st[i].push_back(trCnt);
	}
	trCnt++;
}

void remove(int mId) {
	trr[trMap[mId]].state = 0;
}

int que[205], head, tail, visited[205];
void bfs(int s) {
	head = tail = 0;
	for (int i = 0; i < trCnt; i++) visited[i] = 0;
	visited[s] = 1;
	que[tail++] = s;
	while (head < tail) {
		int cur = que[head++];
		if (!trr[cur].state) continue;
		for (int nx : adj[cur]) {
			if (visited[nx]) continue;
			if (!trr[nx].state) continue;
			visited[nx] = visited[cur] + 1;
			que[tail++] = nx;
		}
	}
}

int calculate(int sId, int eId) {
	int ret = INF;
	if (st[sId].empty() || st[eId].empty()) return -1;
	for (auto s : st[sId]) {
		if (!trr[s].state) continue;
		bfs(s);
		for (auto e : st[eId]) {
			if (!trr[e].state) continue;
			ret = min(ret, visited[e] - 1);
		}
	}
	return ret == INF ? -1 : ret;
}
```
