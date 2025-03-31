```cpp
#if 1 // 113 ms
// Union-find ver.
// 초기에 부모를 자기자신으로 하고 rank는 0으로 입력
// idmap에서의 id를 초기 group id로 입력
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <list>
using namespace std;

#define MAXN 25003
#define INF 1000000001

struct House {
	int mid, x, y;
}house[MAXN];

int p[MAXN], r[MAXN], minHouse[MAXN];
vector<int> group[MAXN];
int find(int x) {
	if (p[x] != x) p[x] = find(p[x]);
	return p[x];
}

int totalCnt;
void unionSet(int a, int b) {
	int rootX = find(a), rootY = find(b);
	if (rootX == rootY) {
		minHouse[rootX] = min(minHouse[rootX], minHouse[rootY]);
		return;
	}
	if (r[rootX] < r[rootY]) swap(rootX, rootY);
	p[rootY] = rootX;
	if (r[rootX] == r[rootY]) r[rootX]++;
	group[rootX].insert(group[rootX].end(), group[rootY].begin(), group[rootY].end());
	minHouse[rootX] = min(minHouse[rootX], minHouse[rootY]);
	totalCnt--;
}

unordered_map<int, int> idMap;
int idCnt;
vector<int> map[103][103];

int L, R;
void init(int L, int R) {
	::L = L, ::R = R, idCnt = totalCnt = 0, idMap.clear();
	for (int i = 0; i < MAXN; i++) group[i].clear(), minHouse[i] = INF;
	for (int i = 0; i < 103; i++) for (int j = 0; j < 103; j++)
		map[i][j].clear();
}

bool near(int aid, int bid) {
	int dist = abs(house[aid].x - house[bid].x) + abs(house[aid].y - house[bid].y);
	return (dist <= L) ? true : false;
}

int add(int mId, int mX, int mY) {
	int id = idMap[mId] = idCnt++;
	house[id] = { mId, mX, mY };
	p[id] = id, r[id] = 0, totalCnt++;
	group[id].push_back(id);
	minHouse[id] = mId;
	int gx = mX / L, gy = mY / L;
	int sX = max(0, gx - 1), eX = min(R / L, gx + 1);
	int sY = max(0, gy - 1), eY = min(R / L, gy + 1);
	for (int i = sX; i <= eX; i++) for (int j = sY; j <= eY; j++)
		for (int nid : map[i][j]) if (near(nid, id)) unionSet(nid, id);
	map[gx][gy].push_back(id);
	return (int)group[find(id)].size();
}

int remove(int mId) { // mId 집이 삭제되더라도, mId 집의 위치에 상관없이 마을을 분할하지 않는다.
	if (!idMap.count(mId)) return -1;
	int id = idMap[mId];
	idMap.erase(mId);
	auto& m = map[house[id].x / L][house[id].y / L];
	int root = find(id);
	group[root].erase(find(group[root].begin(), group[root].end(), id));
	if (minHouse[root] == mId) {
		minHouse[root] = INF;
		for (auto nx : group[root]) minHouse[root] = min(minHouse[root], house[nx].mid);
	}
	m.erase(find(m.begin(), m.end(), id));
	if (group[root].empty()) totalCnt--;
	return (int)group[root].size();
}

int check(int mId) {
	return idMap.count(mId) ? minHouse[find(idMap[mId])] : -1;
}

int count() {
	return totalCnt;
}
#endif // 1
```
