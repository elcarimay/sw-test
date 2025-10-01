```cpp
#if 1 // 128 ms
#include<vector>
#include<unordered_map>
#include<algorithm>
using namespace std;
#define	MXN	1001
#define	MXR	2001

struct Road { int e, len, rid; };
vector<Road>adj[MXN];			//{e, len, Road id, 삭제여부}
int N, rcnt, target;
unordered_map<int, int> hmap;	//id, {s, e}
vector<vector<int>> allPaths[MXN];
vector<int> L[MXN], Path, S;	//Length, path, Sum
bool v[MXR], R[MXR];			//visited, removed

void init(int N) {
	::N = N;	rcnt = 0;	hmap.clear();
	for (auto& a : adj) a.clear();
}

int getIdx(int id) { return hmap.count(id) ? hmap[id] : hmap[id] = rcnt++; }

void addRoad(int K, int mID[], int mSpotA[], int mSpotB[], int mLen[]) {
	for (int i = 0; i < K; i++) {
		int rid = getIdx(mID[i]);
		adj[mSpotA[i]].push_back({ mSpotB[i] , mLen[i], rid });
		adj[mSpotB[i]].push_back({ mSpotA[i] , mLen[i], rid });
		R[rid] = false;
	}
}

void removeRoad(int mID) {
	if (hmap.count(mID) == 0) return;
	R[hmap[mID]] = true;
	hmap.erase(mID);
}

void dfs(int s, int lev, int cost) {
	if (lev == 4) {
		L[s].push_back(cost);
		allPaths[s].push_back(Path);
		return;
	}
	for (auto c : adj[s]) {
		if (R[c.rid] || v[c.rid] || c.e == target) continue;
		v[c.rid] = true;
		Path.push_back(c.rid);
		cost += c.len;
		dfs(c.e, lev + 1, cost);
		Path.pop_back();
		cost -= c.len;
		v[c.rid] = false;
	}
}

bool check(vector<int> a, vector<int> b) {
	for (int i = 0; i < 4; i++)	for (int j = 0; j < 4; j++)
		if (a[i] == b[j]) return true;
	return false;
}

int getLength(int mSpot) {
	for (int i = 1; i <= N; i++) L[i].clear(), allPaths[i].clear();
	memset(v, false, sizeof(v));
	S.clear(); Path.clear();
	target = mSpot;
	dfs(target, 0, 0);
	for (int i = 1; i <= N; i++) {
		if (L[i].size() > 1) {
			int canditate = -1;
			for (int j = 0; j < L[i].size() - 1; j++) {
				for (int k = j + 1; k < L[i].size(); k++) {
					if (check(allPaths[i][j], allPaths[i][k])) continue;
					int sum = L[i][j] + L[i][k];
					if (sum <= 42195) canditate = max(sum, canditate);
				}
			}
			S.push_back(canditate);
		}
	}
	if (S.size()) {
		int rst = *max_element(S.begin(), S.end());
		return rst;
	}
	return -1;
}
#endif // 0

```
