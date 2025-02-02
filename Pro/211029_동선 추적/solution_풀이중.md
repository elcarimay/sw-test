```cpp
#include <set>
#include <vector>
#include <unordered_map>
#include <algorithm>
#include <iostream>
using namespace std;

#define MAXN 10000
#define FRONT true
#define BACK false

unordered_map<int, int> idMap;
int r, c, idCnt, cnt, idx, pid;
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

set<Data> ver[MAXN], ld[MAXN*2], hor[MAXN], rd[MAXN*2];
vector<int> v[1003];
set<Data>::iterator it;
void init(){
	idCnt = 0, idMap.clear();
	for (int i = 0; i < MAXN; i++)
		ver[i].clear(), hor[i].clear(), db[i] = {};
	for (int i = 0; i < MAXN * 2; i++) ld[i].clear(), rd[i].clear();
	for (int i = 0; i < 1000; i++) v[i].clear();
}

void addPlace(int pID, int r, int c){
	db[pID] = { r, c, -1 };
	ver[c].insert({ pID });
	hor[r].insert({ pID });
	ld[r + c].insert({pID});
	(r - c < 0) ? idx = r - c + 10000 : idx = r - c;
	rd[idx].insert({ pID });
}

void removePlace(int pID){
	r = db[pID].r, c = db[pID].c, idx = 0;
	it = lower_bound(ver[c].begin(), ver[c].end(), Data{pID});
	ver[c].erase(it);
	it = lower_bound(hor[r].begin(), hor[r].end(), Data{ pID });
	hor[r].erase(it);
	it = lower_bound(ld[r + c].begin(), ld[r + c].end(), Data{ pID });
	ld[r + c].erase(it);
	(r - c < 0) ? idx = r - c + 10000 : idx = r - c;
	it = lower_bound(rd[idx].begin(), rd[idx].end(), Data{ pID });
	rd[idx].erase(it);
}

void update(int uid, set<Data>::iterator& it, bool opt, int vi[]) {
	opt == FRONT ? it-- : it++;
	pid = it->pid;
	if (db[pid].uid == -1 || db[pid].uid == uid) {
		db[pid].uid = uid, v[idMap[uid]].push_back(pid);
		r = db[pid].r, c = db[pid].c, vi[cnt++] = pid, flag = true;
	}
}

void contactTracing(int uID, int visitNum, int moveInfo[], int visitList[]){
	int id = idMap[uID] = idCnt++;
	pid = moveInfo[0];
	cnt = 0; 
	// 현재위치
	r = db[pid].r, c = db[pid].c, visitList[cnt++] = pid;
	v[id].push_back(pid);
	for (int i = 1; i < visitNum; i++) {
		int d = moveInfo[i]; flag = false; // update 탈출 변수
		if (d == 0) { // 0번방향
			it = lower_bound(ver[c].begin(), ver[c].end(), Data{ pid });
			while (it != ver[c].begin() && !flag) update(uID, it, FRONT, visitList);
		}
		else if (d == 1) {// 1번방향
			it = lower_bound(ld[r + c].begin(), ld[r + c].end(), Data{ pid });
			while (it != ld[r + c].begin() && !flag) update(uID, it, FRONT, visitList);
		}
		else if (d == 2) {// 2번방향
			it = lower_bound(hor[r].begin(), hor[r].end(), Data{ pid });
			while (it != hor[r].end() && !flag) update(uID, it, BACK, visitList);
		}
		else if (d == 3) {// 3번방향
			(r - c < 0) ? idx = r - c + 10000 : idx = r - c;
			it = lower_bound(rd[idx].begin(), rd[idx].end(), Data{ pid });
			while (it != rd[idx].end() && !flag) update(uID, it, BACK, visitList);
		}
		else if (d == 4) {// 4번방향
			it = lower_bound(ver[c].begin(), ver[c].end(), Data{ pid });
			while (it != ver[c].end() && !flag) update(uID, it, BACK, visitList);
		}
		else if (d == 5) {// 5번방향
			it = lower_bound(ld[r + c].begin(), ld[r + c].end(), Data{ pid });
			while (it != ld[r + c].end() && !flag) update(uID, it, BACK, visitList);
		}
		else if (d == 6) {// 6번방향
			it = lower_bound(hor[r].begin(), hor[r].end(), Data{ pid });
			while (it != hor[r].begin() && !flag) update(uID, it, FRONT, visitList);
		}
		else {// 7번방향
			(r - c < 0) ? idx = r - c + 10000 : idx = r - c;
			it = lower_bound(rd[idx].begin(), rd[idx].end(), Data{ pid });
			while (it != rd[idx].begin() && !flag) update(uID, it, FRONT, visitList);
		}
	}
}

void disinfectPlaces(int uID){
	int id = idMap[uID];
	for (auto pid : v[id]) db[pid].uid = -1;
	v[id].clear();
}
```
