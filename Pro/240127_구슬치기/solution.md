```cpp
#if 1
#include <set>
#include <queue>
#include <unordered_map>
using namespace std;

#define MAXN 3000
#define NORMAL 0
#define LD 1
#define RD 2

int r, c, cnt;
bool front, back;
struct DB {
	int r, c;
	bool closed;
}db[33003];

struct Pos {
	int r, c;
	bool operator==(const Pos& re)const {
		return r == re.r && c == re.c;
	}
	bool operator<(const Pos& re)const {
		if (r != re.r) return r < re.r;
		return c < re.c;
	}
};

set<Pos> ver[MAXN], ld[MAXN * 2], hor[MAXN], rd[MAXN * 2];
set<Pos>::iterator it;
int getHash(int r, int c) {
	return r * 10000 + c;
}

unordered_map<int, int> holeMap;

struct Pos2 {
	int minr, maxr, minc, maxc;
};

Pos2 ldRange[MAXN * 2], rdRange[MAXN * 2];

void init(int N) {
	holeMap.clear();
	for (int i = 0; i < MAXN; i++) ver[i].clear(), hor[i].clear();
	for (int i = 0; i < MAXN * 2; i++) ld[i].clear(), rd[i].clear(), ldRange[i] = rdRange[i] = {};
}

void addDiagonal(int mARow, int mACol, int mBRow, int mBCol) {
	int minr, maxr, minc, maxc;
	if (mARow + mACol == mBRow + mBCol) { // left diagonal
		if (mARow > mBRow) minr = mBRow, maxr = mARow, minc = mACol, maxc = mBCol;
		else minr = mARow, maxr = mBRow, minc = mBCol, maxc = mACol;
		ldRange[mARow + mACol] = { minr, maxr, minc, maxc };
	}
	else { // right diagonal
		if (mARow < mBRow) minr = mARow, maxr = mBRow, minc = mACol, maxc = mBCol;
		else minr = mBRow, maxr = mARow, minc = mBCol, maxc = mACol;
		rdRange[MAXN + mARow - mACol] = { minr, maxr, minc, maxc };
	}
}

void addHole(int mRow, int mCol, int mID) {
	db[mID] = { mRow, mCol, false };
	holeMap[getHash(mRow, mCol)] = mID;
	ver[mCol].insert({ mRow, mCol }), hor[mRow].insert({ mRow, mCol });
	ld[mRow + mCol].insert({ mRow, mCol }), rd[mRow - mCol + MAXN].insert({ mRow, mCol });
}

void eraseHole(int mRow, int mCol) {
	int hash = getHash(mRow, mCol);
	if (holeMap.count(hash)) {
		ver[mCol].erase({ mRow, mCol }), hor[mRow].erase({ mRow, mCol });
		ld[mRow + mCol].erase({ mRow, mCol }), rd[mRow - mCol + MAXN].erase({ mRow, mCol });
		holeMap.erase(hash);
	}
}

struct Info {
	int mid, d;
	bool operator<(const Info& re)const {
		if (d != re.d) return d > re.d;
		if (db[mid].r != db[re.mid].r) return db[mid].r > db[re.mid].r;
		return db[mid].c > db[re.mid].c;
	}
};
priority_queue<Info> pq;

int normal_dist(int mid, int r, int c) {
	return abs(db[mid].r - r) * 10 + abs(db[mid].c - c) * 10;
}

int diagonal_dist(int mid, int r, int c) {
	return abs(db[mid].r - r) * 14;
}

void dir(set<Pos>& s) {
	front = back = false;
	it = lower_bound(s.begin(), s.end(), Pos{ r,c });
	if (it == s.end()) front = true; // 앞으로 전진
	if (it == s.begin()) back = true; // 뒤로 전진
	if (it != s.end() && it != s.begin()) front = back = true;
}

bool range(Pos2& p, int r, int c) {
	return p.minr <= r && p.maxr >= r && p.minc <= c && p.maxc >= c;
}

void frontGo(set<Pos>& s, int opt) {
	while (front && it != s.begin()) {
		it--;
		int mid = holeMap[getHash(it->r, it->c)];
		if (db[mid].closed) continue;
		if (opt == NORMAL) {
			pq.push({ mid , normal_dist(mid, r, c) }); return;
		}
		else if(opt == LD) {
			if (range(ldRange[r + c], it->r, it->c)) {
				pq.push({ mid , diagonal_dist(mid, r, c) }); return;
			}
		}
		else {
			if (range(rdRange[MAXN + r - c], it->r, it->c)) {
				pq.push({ mid , diagonal_dist(mid, r, c) }); return;
			}
		}
	}
}

void backGo(set<Pos>& s, int opt) {
	it = lower_bound(s.begin(), s.end(), Pos{ r,c });
	while (back) {
		int mid = holeMap[getHash(it->r, it->c)];
		if (opt == NORMAL) {
			if (!db[mid].closed) {
				pq.push({ mid , normal_dist(mid, r, c) }); return;
			}
		}
		else if (opt == LD) {
			if (!db[mid].closed && range(ldRange[r + c], it->r, it->c)) {
				pq.push({ mid , diagonal_dist(mid, r, c) }); return;
			}
		}
		else {
			if (!db[mid].closed && range(rdRange[MAXN + r - c], it->r, it->c)) {
				pq.push({ mid , diagonal_dist(mid, r, c) }); return;
			}
		}
		if (it == --s.end()) break;
		it++;
	}
}

int hitMarble(int mRow, int mCol, int mK) {
	cnt = 0, r = mRow, c = mCol;
	vector<Info> tmp; int mid, ret = -1;
	while (cnt++ < mK) {
		while (!pq.empty()) pq.pop();
		if (hor[r].size()) dir(hor[r]), frontGo(hor[r], NORMAL), backGo(hor[r], NORMAL);
		if (ver[c].size()) dir(ver[c]), frontGo(ver[c], NORMAL), backGo(ver[c], NORMAL);
		if (ld[r + c].size() && range(ldRange[r + c], r, c))
			dir(ld[r + c]), frontGo(ld[r + c], LD), backGo(ld[r + c], LD);
		if (rd[MAXN + r - c].size() && range(rdRange[MAXN + r - c], r, c))
			dir(rd[MAXN + r - c]), frontGo(rd[MAXN + r - c], RD), backGo(rd[MAXN + r - c], RD);
		if (pq.empty()) break;
		tmp.push_back(pq.top()); ret = pq.top().mid;
		r = db[ret].r, c = db[ret].c, db[ret].closed = true;
	}
	for (auto nx : tmp) db[nx.mid].closed = false;
	return ret;
}
#endif // 1
```
