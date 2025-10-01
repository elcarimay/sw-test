```cpp
#if 1 // 222 ms
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;

#define MAXR 2003
#define MAXN 1003

unordered_map<int, int> hmap;
int idCnt, N;
bool used[MAXR], removed[MAXR];

struct Edge {
	int to, len, rid;
};
vector<Edge> adj[MAXN];

void init(int N) {
	::N = N, idCnt = 0, hmap.clear();
	memset(removed, 0, sizeof(removed));
	for (int i = 1; i <= N; i++) adj[i].clear();
}

void addRoad(int K, int mID[], int mSpotA[], int mSpotB[], int mLen[]) {
	for (int i = 0; i < K; i++) {
		int rid = hmap[mID[i]] = idCnt++;
		adj[mSpotA[i]].push_back({ mSpotB[i], mLen[i], rid });
		adj[mSpotB[i]].push_back({ mSpotA[i], mLen[i], rid });
	}
}

void removeRoad(int mID) {
	if (!hmap.count(mID)) return;
	int id = hmap[mID];
	hmap.erase(mID);
	if (removed[id]) return;
	removed[id] = true;
}

struct Info {
	int sumLen, rids[4];
	bool operator==(const Info& r) const {
		return sumLen == r.sumLen && rids == r.rids;
	}
};

struct Data {
	int to, depth, sumLen, rids[4];
};

int getLength(int mSpot) {
	vector<Info> info[MAXN];
	queue<Data> q;
	q.push({ mSpot, 0, 0 });
	while (!q.empty()) {
		auto cur = q.front(); q.pop();
		if (cur.depth == 4) {
			info[cur.to].push_back({ cur.sumLen });
			int size = (int)info[cur.to].size();
			for (int i = 0; i < cur.depth; i++) info[cur.to][size - 1].rids[i] = cur.rids[i];
			continue;
		}
		memset(used, 0, sizeof(used));
		for (int i = 0; i < cur.depth; i++) used[cur.rids[i]] = true;
		for (Edge nx : adj[cur.to]) {
			if (removed[nx.rid]) continue;
			if (used[nx.rid]) continue;
			Data tmp = { nx.to, cur.depth + 1, cur.sumLen + nx.len };
			for (int i = 0; i < cur.depth; i++) tmp.rids[i] = cur.rids[i];
			tmp.rids[cur.depth] = nx.rid;
			q.push(tmp);
		}
	}
	int globalmax = -1;
	for (int i = 1; i <= N; i++) {
		int size = (int)info[i].size();
		if (size == 1) continue;
		for (int j = 0; j < size; j++) for (int k = j + 1; k < size; k++) {
			auto& in1 = info[i][j];
			auto& in2 = info[i][k];
			memset(used, 0, sizeof(used));
			for (int rid : in1.rids) used[rid] = true;
			bool collision = false;
			for (int rid : in2.rids) if (used[rid]) {
				collision = true; break;
			}
			if (collision) continue;
			int sumLen = in1.sumLen + in2.sumLen;
			if (sumLen <= 42195) globalmax = max(globalmax, sumLen);
		}
	}
	return globalmax;
}
#endif // 1
```
