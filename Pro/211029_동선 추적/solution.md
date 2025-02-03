```cpp
#if 1 // set으로 비교시 end()가 아닌 --end()로 비교, 일단 현재위치를 탐색한 것으로 처리하고 진행해야 함, set에서 구조체 지워짐
// Fig2, 3가 문제설명과 다름
#include <set>
#include <unordered_map>
using namespace std;

#define MAXN 10000
#define FRONT true
#define BACK false

unordered_map<int, int> idMap;
int r, c, idCnt, cnt, pid;
bool flag;
struct DB {
	int r, c, uid;
}db[50003];

struct Data {
	int pid;
	bool operator<(const Data& re)const {
		if (db[pid].r != db[re.pid].r) return db[pid].r < db[re.pid].r;
		return db[pid].c < db[re.pid].c;
	}
};

set<Data> ver[MAXN], ld[MAXN * 2], hor[MAXN], rd[MAXN * 2];
set<int> v[1003];
set<Data>::iterator it;
void init() {
	idCnt = 0, idMap.clear();
	for (int i = 0; i < MAXN; i++) ver[i].clear(), hor[i].clear();
	for (int i = 0; i < MAXN * 2; i++) ld[i].clear(), rd[i].clear();
	for (int i = 0; i < 1000; i++) v[i].clear();
}

void addPlace(int pID, int r, int c) {
	db[pID] = { r, c, -1 };
	ver[c].insert({ pID }), hor[r].insert({ pID });
	ld[r + c].insert({ pID }), rd[r - c + 10000].insert({ pID });
}

void removePlace(int pID) {
	r = db[pID].r, c = db[pID].c;
	ver[c].erase(Data{ pID }); hor[r].erase(Data{ pID });
	ld[r + c].erase(Data{ pID }); rd[r - c + 10000].erase(Data{ pID });
}

void update(set<Data>& s, int uid, bool opt, int vi[]) {
	if (s.size() == 1) return;
	it = lower_bound(s.begin(), s.end(), Data{ pid });
	if (opt && it == s.begin()) return;
	if (!opt && it == --s.end()) return;
	while (true) {
		opt ? it-- : it++;
		pid = it->pid;
		if (db[pid].uid == -1 || db[pid].uid == uid) {
			db[pid].uid = uid, v[idMap[uid]].insert(pid);
			r = db[pid].r, c = db[pid].c, vi[cnt++] = pid; return;
		}
		if (it == s.begin() || it == --s.end()) return;
	}
}

void contactTracing(int uID, int visitNum, int moveInfo[], int visitList[]) {
	int id = idMap[uID] = idCnt++;
	pid = moveInfo[0], cnt = 0; // 현재 위치
	db[pid].uid = uID, v[id].insert(pid); 
	r = db[pid].r, c = db[pid].c, visitList[cnt++] = pid;
	for (int i = 1; i < visitNum; i++) {
		int d = moveInfo[i];
		if (d == 0) update(ver[c], uID, FRONT, visitList);
		if (d == 1) update(ld[r + c], uID, FRONT, visitList);
		if (d == 2) update(hor[r], uID, BACK, visitList);
		if (d == 3) update(rd[r - c + 10000], uID, BACK, visitList);
		if (d == 4) update(ver[c], uID, BACK, visitList);
		if (d == 5) update(ld[r + c], uID, BACK, visitList);
		if (d == 6) update(hor[r], uID, FRONT, visitList);
		if (d == 7) update(rd[r - c + 10000], uID, FRONT, visitList);
	}
}

void disinfectPlaces(int uID) {
	int id = idMap[uID];
	for (auto nx_pid : v[id]) db[nx_pid].uid = -1;
}
#endif // 1
```
