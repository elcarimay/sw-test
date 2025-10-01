```cpp
#if 1 // 300 ms
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <queue>
using namespace std;

#define MAXN 20003

int idCnt, L, N, GN, gSum[MAXN];
unordered_map<int, int> hmap;
unordered_set<int> gName;

struct Camp {
	int mid, r, c, q, gid;
}camp[MAXN];

vector<int> g[31][31], gList[MAXN];
void init(int L, int N) {
	::L = L, ::N = N, idCnt = 0, GN = N / L, hmap.clear(), gName.clear();
	for (int i = 0; i < MAXN; i++) gSum[i] = 0, gList[i].clear();
	for (int i = 0; i < GN; i++) for (int j = 0; j < GN; j++) g[i][j].clear();
}

int p[MAXN], r[MAXN];
int find(int x) {
	if (p[x] != x) p[x] = find(p[x]);
	return p[x];
}

void unionSet(int a, int b) {
	int rootX = find(a), rootY = find(b);
	if (rootX == rootY) return;
	if (r[rootX] < r[rootY]) swap(rootX, rootY);
	p[rootY] = rootX;
	if (r[rootX] == r[rootY]) r[rootX]++;
	gList[rootX].insert(gList[rootX].end(), gList[rootY].begin(), gList[rootY].end());
	gName.erase(rootY);
	gSum[rootX] += gSum[rootY];
	gSum[rootY] = 0;
	gList[rootY].clear();
	camp[a].gid = camp[b].gid = rootX;
}

int addBaseCamp(int mID, int mRow, int mCol, int mQuantity) {
	int id = hmap[mID] = idCnt++;
	camp[id] = { mID, mRow, mCol, mQuantity, id };
	p[id] = id, r[id] = 0;
	gSum[id] += mQuantity, gName.insert(id), gList[id].push_back(id);
	int sr = max(0, mRow / L - 1), er = min(GN, mRow / L + 1);
	int sc = max(0, mCol / L - 1), ec = min(GN, mCol / L + 1);
	for (int i = sr; i <= er; i++) for (int j = sc; j <= ec; j++) for (auto bid : g[i][j])
		if (abs(mRow - camp[bid].r) + abs(mCol - camp[bid].c) <= L)
			unionSet(id, bid);
	g[mRow / L][mCol / L].push_back(id);
	return gSum[camp[id].gid];
}

struct Data {
	int id;
	bool operator<(const Data& r)const {
		auto& a = camp[id];
		auto& b = camp[r.id];
		if (a.q != b.q) return a.q > b.q;
		if (a.r != b.r) return a.r > b.r;
		return a.c > b.c;
	}
};

int findBaseCampForDropping(int K) {
	priority_queue<Data> pq;
	for (int gid : gName) if (gSum[gid] >= K)
		for (int id : gList[gid]) pq.push({ id });
	return pq.empty() ? -1 : camp[pq.top().id].mid;
}
#endif // 1
```
