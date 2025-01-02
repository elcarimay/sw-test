```cpp
#if 1
#include <unordered_map>
#include <vector>
#include <algorithm>
using namespace std;
#define MAX_N 999
#define ADDED 1
#define REMOVED 2
struct Pos {
	int r, c, state; // 지워졌던 mID가 또 호출되었을경우 동작하지 않아야 하므로 state변수추가
};
unordered_map<int, vector<Pos>> hmap; // hash, position
unordered_map<int, int> idmap; // mid, hash
int idmapCnt;
Pos db[20002];
Pos d[5] = { {0,0}, {0,2}, {1,1}, {2,0}, {2,2} };
int visit[MAX_N][MAX_N];
bool nut;
vector<Pos> tmp;

int getHash(int (*map)[MAX_N], int r, int c) {
	int hash = 0;
	for (int i = 0; i < 5; i++) {
		hash += map[r + d[i].r][c + d[i].c];
		if (i != 4) hash *= 100;
	}
	return hash;
}

void init(int N, int mInfo[MAX_N][MAX_N]) {
	hmap.clear(); idmapCnt = 0; idmap.clear();
	for (int r = 0; r < MAX_N; r++) for (int c = 0; c < MAX_N; c++) {
		if (r < MAX_N - 2 && c < MAX_N - 2)
			if (mInfo[r][c] != 0) hmap[getHash(mInfo, r, c)].push_back({ r,c });
		visit[r][c] = false;
	}
}

bool cmp(Pos& a, Pos& b) {
	if (a.r != b.r) return a.r < b.r;
	return a.c < b.c;
}

int getHash(int (*tile)[3], int num) { // false 볼트만 존재, true 너트가 존재 
	int hash = 0, add = 0;
	for (int i = 0; i < 5; i++) {
		int value = tile[d[i].r][d[i].c];
		if (value < 6) value += 10;
		else {
			nut = true;
			if (num == 0) value -= 10;
			else value = num;
		}
		hash += value;
		if(i != 4) hash *= 100;
	}
	return hash;
}

bool visited(int r, int c) {
	for (int i = 0; i < 5; i++)	if (visit[r + d[i].r][c + d[i].c]) return true;
	return false;
}

void findPos(int hash) {
	if (hmap.find(hash) == hmap.end()) return;
	for (auto nx : hmap[hash]) if (!visited(nx.r, nx.c)) tmp.push_back(nx);
}

int addRectTile(int mID, int mTile[3][3]) {
	tmp.clear(); nut = false;
	findPos(getHash(mTile, 0));
	if (nut) for (int n = 11; n <= 15; n++) findPos(getHash(mTile, n));
	if (tmp.empty()) return -1;
	if (tmp.size() != 1) partial_sort(tmp.begin(), tmp.begin() + 1, tmp.end(), cmp);
	for (int i = 0; i < 5; i++) visit[tmp[0].r + d[i].r][tmp[0].c + d[i].c] = true;
	idmap[mID] = idmapCnt;
	db[idmapCnt++] = { tmp[0].r, tmp[0].c, ADDED };
	return tmp[0].r * 10000 + tmp[0].c;
}

void removeRectTile(int mID) {
	if (idmap.find(mID) == idmap.end()) return;
	int id = idmap[mID];
	if (db[id].state == REMOVED) return;
	for (int i = 0; i < 5; i++) visit[db[id].r + d[i].r][db[id].c + d[i].c] = false;
	db[id].state = REMOVED;
}
#endif // 1

```
