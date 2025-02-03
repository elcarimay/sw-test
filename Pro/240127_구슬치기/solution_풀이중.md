```cpp
#include <set>
#include <queue>
#include <unordered_map>
using namespace std;

#define MAXN 3000
#define FRONT true
#define BACK false

unordered_map<int, int> idMap;
int r, c, idCnt, cnt, pid;
bool flag;
struct DB {
	int r, c;
	bool closed;
}db[50003];

struct Data {
	int mid;
	bool operator<(const Data& re)const {
		if (db[mid].r != db[re.mid].r) return db[pid].r < db[re.mid].r;
		return db[mid].c < db[re.mid].c;
	}
};

set<Data> ver[MAXN], ld[MAXN * 2], hor[MAXN], rd[MAXN * 2];
set<int> v[1003];
set<Data>::iterator it;

struct Pos {
	int r, c;
};

unordered_map<Pos, int> holeMap;

struct Pos2 {
	int minr, maxr, minc, maxc;
};

Pos2 ldRange[MAXN * 2], rdRange[MAXN * 2];
int cnt;
void init(int N){
	idCnt = 0, idMap.clear(), holeMap.clear();
	for (int i = 0; i < MAXN; i++) ver[i].clear(), hor[i].clear();
	for (int i = 0; i < MAXN * 2; i++) ld[i].clear(), rd[i].clear();
	for (int i = 0; i < 1000; i++) v[i].clear();
}

void addDiagonal(int mARow, int mACol, int mBRow, int mBCol){
	bool flag; // ld인지 rd인지 판별
	(mARow - mBRow)* (mACol - mBCol) > 0 ? flag = true : flag = false; // true면 rd
	int minr, maxr, minc, maxc;
	if (flag) { // rd
		mARow < mBRow ? minr = mARow, maxr = mBRow, minc = mACol, maxc = mBCol : 
			minr = mBRow, maxr = mARow, minc = mBCol, maxc = mACol;
		rdRange[MAXN + mARow - mACol] = { minr, maxr, minc, maxc };
	}
	else { // ld
		mARow > mBRow ? minr = mBRow, maxr = mARow, minc = mACol, maxc = mBCol :
			minr = mARow, maxr = mBRow, minc = mBCol, maxc = mACol;
		ldRange[mARow + mACol] = { minr, maxr, minc, maxc };
	}
}

void addHole(int mRow, int mCol, int mID){
	db[mID] = { mRow, mCol, false };
	holeMap[Pos{ mRow,mCol }] = mID;
	ver[mCol].insert({ mID }), hor[mRow].insert({ mID });
	ld[mRow + mCol].insert({ mID }), rd[mRow - mCol + MAXN].insert({ mID });
}

void eraseHole(int mRow, int mCol){
	int mID = holeMap[Pos{ mRow, mCol }];
	ver[mCol].insert({ mID }), hor[mRow].insert({ mID });
	ld[mRow + mCol].insert({ mID }), rd[mRow - mCol + MAXN].insert({ mID });
	holeMap.erase(Pos{ mRow, mCol });
}

int r, c;
struct Pos2 {
	int mid, d;
	bool operator<(const Pos2& re)const {
		if (d != re.d) return d > re.d;
		if (db[mid].r != db[re.mid].r) return db[mid].r > db[re.mid].r;
		return db[re.mid].c > db[re.mid].c;
	}
};
priority_queue<Pos2> pq;
void update(set<Data>& s) {
	if (s.size() == 1) return;
	it = lower_bound(s.begin(), s.end(), Data{ pid });
	while (it != s.begin()) {
		it--;

	}
	if (it == --s.end()) return;
	while (true) {
		opt ? it-- : it++;
		pid = it->mid;
		if (!db[pid].closed) {
			pq.push(mid);
			return;
		}
		if (it == s.begin() || it == --s.end()) return;
	}
}

int hitMarble(int mRow, int mCol, int mK){
	while (!pq.empty()) pq.pop();
	Pos cur = { mRow, mCol };
	// 8가지 방향에 대해 조사
	int mid;
	cnt = 1, r = mRow, c = mCol;
	while (true) {
		update(ver[c], FRONT);
		update(ld[r + c], FRONT);
		update(hor[r], BACK);
		update(rd[r - c + 10000], BACK);
		update(ver[c], BACK);
		update(ld[r + c], BACK);
		update(hor[r], FRONT);
		update(rd[r - c + 10000], FRONT);
		if (cnt++ == mK) return mid;
	}
}
```
