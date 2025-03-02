```cpp
#if 1
// 전체 map이 1000 x 1000 이므로 그대로 맵에 unordered_map에 의한 mCnt로 id를 입력
// 주위4개에 대한 문명파악할때 unordered_map으로 입력후 우큐로 선택
// 각 문명당 list로 입력하고 mID2가 작을때는 관리하는 ID를 서로 바꾸고 큰 문명에 작은 문명 list를 splice로 이어붙임
#include <vector>
#include <list>
#include <algorithm>
#include <unordered_map>
#include <queue>
#include <string.h>
using namespace std;

struct DB {
	int r, c;
}db;

unordered_map<int, int> m, rm;
int mCnt;
list<DB> l[20003];
int N, map[1003][1003];

void init(int N) {
	::N = N, mCnt = 1, m.clear(), rm.clear();
	for (int i = 1; i <= 1000; i++) for (int j = 1; j <= 1000; j++) map[i][j] = 0;
}

int dr[] = { 0,-1,0,1 }, dc[] = { 1,0,-1,0 };
struct Data {
	int id, cnt;
	bool operator<(const Data& r)const {
		if (cnt != r.cnt) return cnt < r.cnt;
		return id > r.id;
	}
};

int getID(int c) {
	if (m.count(c)) return m[c];
	rm[mCnt] = c;
	l[mCnt].clear();
	return m[c] = mCnt++;
}
int newCivilization(int r, int c, int mID) { // 지도에는 mCnt를 입력, unordered_map에는 실제 MID를 입력
	unordered_map<int, int> neighbor;
	for (int i = 0; i < 4; i++) {
		int nr = r + dr[i], nc = c + dc[i];
		if (map[nr][nc]) neighbor[map[nr][nc]]++;
	}
	int mid = mID;
	priority_queue<Data> pq;
	if (!neighbor.empty()) {
		for (auto nx : neighbor) pq.push({ rm[nx.first], nx.second });
		mid = pq.top().id;
	}
	l[map[r][c] = getID(mid)].push_back({ r, c });
	return rm[map[r][c]];
}

void change(int id1, int id2, bool flag) {
	for (list<DB>::iterator it = l[id2].begin(); it != l[id2].end(); it++)
		map[it->r][it->c] = id1;
	if(flag) l[id1].splice(l[id1].end(), l[id2]);
}

int removeCivilization(int mID) {
	if (!m.count(mID)) return 0;
	int id = getID(mID);
	m.erase(mID);
	change(0, id, false);
	return l[id].size();
}

int getCivilization(int r, int c) {
	return rm[map[r][c]];
}

int getCivilizationArea(int mID) {
	return l[getID(mID)].size();
}

int mergeCivilization(int mID1, int mID2) {
	int id1 = getID(mID1), id2 = getID(mID2);
	if (l[id1].size() < l[id2].size())
		change(id2, id1, true), rm[m[mID1] = id2] = mID1;
	else change(id1, id2, true);
	m.erase(mID2);
	return l[m[mID1]].size();
}
#endif // 1
```
