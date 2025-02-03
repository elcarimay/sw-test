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

vector<int> ver[MAXN], ld[MAXN * 2], hor[MAXN], rd[MAXN * 2];

struct Pos {
	int r, c;
	bool operator==(const Pos& re)const {
		return r == re.r && c == re.c;
	}
};

unordered_map<Pos, int> holeMap;

struct Pos2 {
	int minr, maxr, minc, maxc;
};

Pos2 ldRange[MAXN * 2], rdRange[MAXN * 2];
void init(int N) {
	//idCnt = 0, idMap.clear(), holeMap.clear();
	for (int i = 0; i < MAXN; i++) ver[i].clear(), hor[i].clear();
	for (int i = 0; i < MAXN * 2; i++) ld[i].clear(), rd[i].clear();
}

void addDiagonal(int mARow, int mACol, int mBRow, int mBCol) {
	//bool flag; // ld인지 rd인지 판별
	//(mARow - mBRow)* (mACol - mBCol) > 0 ? flag = true : flag = false; // true면 rd
	//int minr, maxr, minc, maxc;
	//if (flag) { // rd
	//	mARow < mBRow ? minr = mARow, maxr = mBRow, minc = mACol, maxc = mBCol :
	//		minr = mBRow, maxr = mARow, minc = mBCol, maxc = mACol;
	//	rdRange[MAXN + mARow - mACol] = { minr, maxr, minc, maxc };
	//}
	//else { // ld
	//	mARow > mBRow ? minr = mBRow, maxr = mARow, minc = mACol, maxc = mBCol :
	//		minr = mARow, maxr = mBRow, minc = mBCol, maxc = mACol;
	//	ldRange[mARow + mACol] = { minr, maxr, minc, maxc };
	//}
}

void addHole(int mRow, int mCol, int mID) {
	/*db[mID] = { mRow, mCol, false };
	holeMap[Pos{ mRow,mCol }] = mID;
	ver[mCol].push_back( mID ), hor[mRow].push_back(mID );
	ld[mRow + mCol].push_back(mID), rd[mRow - mCol + MAXN].push_back(mID);*/
}

void eraseHole(int mRow, int mCol) {
	/*int mID = holeMap[Pos{ mRow, mCol }];
	ver[mCol].push_back(mID), hor[mRow].push_back(mID);
	ld[mRow + mCol].push_back(mID), rd[mRow - mCol + MAXN].push_back(mID);
	holeMap.erase(Pos{ mRow, mCol });*/
}

struct Info {
	int mid, d;
	bool operator<(const Info& re)const {
		if (d != re.d) return d > re.d;
		if (db[mid].r != db[re.mid].r) return db[mid].r > db[re.mid].r;
		return db[re.mid].c > db[re.mid].c;
	}
};

int normal_dist(int r, int c, int mid) {
	return abs(db[mid].r - r) * 10 + abs(db[mid].c - c) * 10;
}

int diagonal_dist(int r, int c, int mid) {
	return abs(db[mid].r - r) * 14;
}


int hitMarble(int mRow, int mCol, int mK) {
	priority_queue<Info> pq;
	int cnt = 1, r = mRow, c = mCol;
	/*for (auto mid : hor[r])
		if(!db[mid].closed) pq.push({ mid, normal_dist(r,c,mid) });
	for (auto mid : ver[c])
		if (!db[mid].closed) pq.push({ mid, normal_dist(r,c,mid) });
	for(auto mid: ld[r + c])
		if (!db[mid].closed) {
			int row = db[mid].r, col = db[mid].c;
			if(ldRange[r + c].minr<= row && ldRange[r + c].maxr >= row &&
				ldRange[r + c].minc <= row && ldRange[r + c].maxc >= col)
				pq.push({ mid, diagonal_dist(r,c,mid) });
		}
	for (auto mid : rd[r + c])
		if (!db[mid].closed) {
			int row = db[mid].r, col = db[mid].c;
			if (rdRange[MAXN + r + c].minr <= row && rdRange[MAXN + r + c].maxr >= row &&
				rdRange[MAXN + r + c].minc <= row && rdRange[MAXN + r + c].maxc >= col)
				pq.push({ mid, diagonal_dist(r,c,mid) });
		}
	while (!pq.empty()) {
		Info cur = pq.top(); pq.pop();
		if (db[cur.mid].closed) continue;
		if (cnt++ == mK) return cur.mid;
	}*/
	return -1;
}
```
