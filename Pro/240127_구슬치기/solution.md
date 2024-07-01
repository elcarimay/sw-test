```cpp
#include <cmath>
#include <unordered_map>
#include <unordered_set>
#include <vector>
#include <algorithm>
using namespace std;
using pii = pair<int, int>;

#define LM 6005 // 이동가능한 길이
#define HLM 30005 // 홀갯수
int N;

pii leftRange[LM], rightRange[LM]; // 왼쪽/오른쪽 대각선 길의 이동가능한 구간
unordered_set<int> leftList[LM];  // leftD(/)  : 왼쪽 대각선에 있는 점들 목록
unordered_set<int> rightList[LM]; // rightD(\) : 오른쪽 대각선에 있는 점들 목록
unordered_set<int> hori[LM];      // 수평선 길에 있는 점들 목록
unordered_set<int> vert[LM];      // 수직선 길에 있는 점들 목록
unordered_set<int> leftRoad[LM];  // leftD(/)  : 왼쪽 대각선 길에 있는 점들 목록
unordered_set<int> rightRoad[LM]; // rightD(\) : 오른쪽 대각선 길에 있는 점들 목록
unordered_map<int, int> hmap;

struct Hole {
	int r, c, closed, L, R;
	void init(int nr, int nc) {
		r = nr, c = nc, closed = 0;
		L = r + c, R = N + r - c;
	}
	int operator-(const Hole& hole)const {
		if (r == hole.r) return abs(c - hole.c) * 10;
		if (c == hole.c) return abs(r - hole.r) * 10;
		return abs(r - hole.r) * 14;
	}
	bool operator<(const Hole& hole)const {
		return r != hole.r ? r < hole.r : c < hole.c;
	}
}hole[HLM];

void init(int N){
	::N = N, hmap.clear();
	for (int i = 0; i <= N * 2; ++i) {
		leftRange[i] = { 0, 0 }, rightRange[i] = { 0, 0 };
		leftList[i].clear(), rightList[i].clear();
		hori[i].clear(), vert[i].clear();
		leftRoad[i].clear(), rightRoad[i].clear();
	}
}

void addDiagonal(int mARow, int mACol, int mBRow, int mBCol){
	if (mARow + mACol == mBRow + mBCol) { // 왼쪽 대각선(/) 길이라면
		int L = mARow + mACol;
		int st = N + mARow - mACol;
		int ed = N + mBRow - mBCol;
		if (st > ed) swap(st, ed);
		leftRange[L] = { st, ed };
		for (auto id : leftList[L])
			if (st <= hole[id].R && hole[id].R <= ed)
				leftRoad[L].insert(id);
	}
	else {
		int R = N + mARow - mACol;
		int st = mARow + mACol;
		int ed = mBRow + mBCol;
		if (st > ed) swap(st, ed);
		rightRange[R] = { st,ed };
		for (auto id : rightList[R])
			if (st <= hole[id].L && hole[id].L <= ed)
				rightRoad[R].insert(id);
	}
}

void addHole(int mRow, int mCol, int mID){
	hole[mID].init(mRow, mCol);
	hori[mRow].insert(mID), vert[mCol].insert(mID);

	int L = mRow + mCol, R = N + mRow - mCol;
	leftList[L].insert(mID), rightList[R].insert(mID);
	if (leftRange[L].first <= R && R <= leftRange[L].second)
		leftRoad[L].insert(mID);
	if (rightRange[R].first <= L && L <= rightRange[R].second)
		rightRoad[R].insert(mID);
	hmap[(mRow << 16) | mCol] = mID;
}

void eraseHole(int mRow, int mCol){
	auto it = hmap.find((mRow << 16) | mCol);
	if (it == hmap.end()) return;
	int id = it->second;
	int L = mRow + mCol, R = N + mRow - mCol;
	hori[mRow].erase(id), vert[mCol].erase(id);
	leftList[L].erase(id), rightList[R].erase(id);
	leftRoad[L].erase(id), rightRoad[R].erase(id);
	hmap.erase(id);
}

int holeID, holeDist;
void search(unordered_set<int>& s) {
	for (auto id : s) {
		if (hole[id].closed) continue;
		int dist = hole[0] - hole[id]; // 구슬과 홀 사이거리
		if (holeDist > dist) holeID = id, holeDist = dist; // 1우선순위: 거리
		else if (holeDist == dist && hole[id] < hole[holeID]) // 2우선순위 r, 3우선순위 c
			holeID = id;
	}
}

int hitMarble(int mRow, int mCol, int mK){
	vector<int> closedHoleList;
	int retID = -1;
	hole[0].init(mRow, mCol); // 현재 구슬 위치를 Hole객체로 생성
	auto& tg = hole[0]; // 구슬의 현재 위치
	for (int i = 0; i < mK; i++) {
		int L = tg.r + tg.c, R = N + tg.r - tg.c; // 구슬의 대각선 번호
		holeID = -1, holeDist = 1 << 20; // 구슬을 넣을 홀아이디와 거리
		search(hori[tg.r]);
		search(vert[tg.c]);
		if (leftRange[L].first <= R && R <= leftRange[L].second)
			search(leftRoad[L]);
		if (rightRange[R].first <= L && L <= rightRange[R].second)
			search(rightRoad[R]);
		if (holeID < 0) break;
		closedHoleList.push_back(holeID);
		hole[holeID].closed = 1;
		tg = hole[holeID];
		retID = holeID;
	}
	for (auto id : closedHoleList)
		hole[id].closed = 0;
	return retID;
}
```
