```cpp
#include <vector>
#include <string.h>
#include <unordered_map>
#include <queue>
#include <list>
using namespace std;

int map[1003][1003];
int dr[] = { 0,-1,0,1 }, dc[] = { 1,0,-1,0 };
unordered_map<int, int> idMap; // mid, id
unordered_map<int, vector<int>> g; // gid, {r,c}
int idCnt, gCnt;

list<int> li[60003];

struct Data {
	int id, cnt;
	bool operator<(const Data& r)const {
		if (cnt != r.cnt) return cnt < r.cnt;
		return id > r.id;
	}
};

int N;
void init(int N) {
	::N = N;
	memset(map, 0, sizeof(map));
	idCnt = 0, idMap.clear(), gCnt = 1, g.clear();
	for (int i = 1; i <= 60000; i++) li[i].clear();
}

int newCivilization(int r, int c, int mID) {
	priority_queue<Data> pq;
	int cnt = 0;
	for (int i = 0; i < 4; i++) {
		int nr = r + dr[i], nc = c + dc[i];
		if (nr <= 0 || nr > N || nc <= 0 || nc > N) continue;
		idMap[map[nr][nc]]++, cnt++;
	}
	for (auto nx : idMap) pq.push({ nx.first, nx.second });
	int id;
	if (pq.top().id == 0 && pq.top().cnt == cnt)
		idMap[mID] = idCnt++, id = mID;
	else {
		if (pq.top().id == 0) pq.pop();
		id = pq.top().id;
	}
	li[idMap[mID]].push_back({ r * 1000 + c });
	//g[id].push_back({ r * 1000 + c });
	return map[r][c] = id;
}

int removeCivilization(int mID) {
	if (!idMap.count(mID)) return 0;
	int id = idMap[mID];
	int ret = li[id].size();
	/*int ret = g[mID].size();
	if (!ret) return 0;
	for (auto nx : g[mID]) {
		int r = nx / 1000, c = nx % 1000;
		map[r][c] = 0;
	}
	g.erase(mID);*/
	for (list<int>::iterator it = li[id].begin(); it != li[id].end(); it++) {
		int r = *it / 1000, c = *it % 1000;
		map[r][c] = 0;
	}
	li[id].clear();
	return ret;
}

int getCivilization(int r, int c) {
	return map[r][c];
}

int getCivilizationArea(int mID) {
	//return g[mID].size();
	if (!idMap.count(mID)) return 0;
	return li[idMap[mID]].size();
}

int mergeCivilization(int mID1, int mID2) {
	/*if (g[mID1].size() > g[mID2].size()) {
		g[mID1].insert(g[mID1].end(), g[mID2].begin(), g[mID2].end());
		for (auto nx : g[mID2]) {
			int r = nx / 1000, c = nx % 1000;
			map[r][c] = mID2;
		}
		g[mID2].clear();
		return g[mID1].size();
	}
	g[mID2].insert(g[mID2].end(), g[mID1].begin(), g[mID1].end());
	for (auto nx : g[mID1]) {
		int r = nx / 1000, c = nx % 1000;
		map[r][c] = mID1;
	}
	g[mID1].clear();
	return g[mID2].size();*/
	int id1 = idMap[mID1], id2 = idMap[mID2];
	if (li[id1].size() < li[id2].size()) swap(id1, id2);
	for (list<int>::iterator it = li[id2].begin(); it != li[id2].end(); it++) {
		int r = *it / 1000, c = *it % 1000;
		map[r][c] = mID1;
	}
	li[id1].splice(li[id1].end(), li[id2]);
	return li[id1].size();
}

```
