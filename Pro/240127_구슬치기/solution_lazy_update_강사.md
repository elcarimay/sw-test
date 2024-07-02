```cpp
#if 1 // 111 ms
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
/*
j6190_TS구슬치기_user_ver01
lazy update
*/
#include <cmath>
#include <vector>
#include <algorithm>
using namespace std;

using pii = pair<int, int>;
constexpr int LM = 6005;    // > 3000 * 2
constexpr int HLM = 30005;
int N;                      // 평원의 크기

struct Hole {
	int r, c, closed, del;  // 좌표, 닫힘?, 삭제됨?
	int L, R;               // 대각선 좌표
	void init(int nr, int nc) {
		r = nr, c = nc, closed = 0, del = 0;
		L = r + c, R = N + r - c;
	}
	int operator-(const Hole& t)const {  // 두 지점사이 이동비용 계산
		if (r == t.r) return abs(c - t.c) * 10;
		if (c == t.c) return abs(r - t.r) * 10;
		return abs(r - t.r) * 14;
	}
	bool operator<(const Hole& t)const { // 위치 우선순위
		return r != t.r ? r < t.r : c < t.c;
	}
}hole[HLM];

pii leftRange[LM];                 // 왼쪽 대각선 길의 이동가능한 구간(있다면 1개 뿐이다.)
pii rightRange[LM];                // 오른쪽 대각선 길의 이동가능한 구간(있다면 1개 뿐이다.)
vector<int> leftList[LM];          // leftD(/)  : 왼쪽 대각선에 있는 점들 목록
vector<int> rightList[LM];         // rightD(\) : 오른쪽 대각선에 있는 점들 목록

vector<int> hori[LM];              // 수평선 길에 있는 점들 목록
vector<int> vert[LM];              // 수직선 길에 있는 점들 목록
vector<int> leftRoad[LM];          // leftD(/)  : 왼쪽 대각선 길에 있는 점들 목록
vector<int> rightRoad[LM];         // rightD(\) : 오른쪽 대각선 길에 있는 점들 목록

void init(int mN)
{
	for (int i = 0; i <= LM; ++i) {
		leftRange[i] = { 0, 0 };   // 왼쪽 대각선 길의 이동가능한 구간
		rightRange[i] = { 0, 0 };  // 오른쪽 대각선 길의 이동가능한 구간
		leftList[i].clear();       // 왼쪽 대각선 목록 초기화
		rightList[i].clear();      // 오른쪽 대각선 목록 초기화

		hori[i].clear();           // 수평 길 초기화
		vert[i].clear();           // 수직 길 초기화
		leftRoad[i].clear();       // 왼쪽 대각선 길 초기화
		rightRoad[i].clear();      // 오른쪽 대각선 길 초기화
	}
	N = mN;
}

void addDiagonal(int mARow, int mACol, int mBRow, int mBCol)
{
	if (mARow + mACol == mBRow + mBCol) { // 왼쪽 대각선(/) 길이라면
		int L = mARow + mACol;
		int st = N + mARow - mACol;       // 구간의 시작
		int ed = N + mBRow - mBCol;       // 구간의 마지막
		if (st > ed) swap(st, ed);        // "구간의 시작 <= 구간의 마지막"이 되도록 하기
		leftRange[L] = { st, ed };        // 왼쪽 대각선(/) 길 구간정보 기록
		for (auto id : leftList[L]) {
			if (st <= hole[id].R && hole[id].R <= ed)
				leftRoad[L].push_back(id);
		}
	}
	else {                                // 오른쪽 대각선(/) 길이라면
		int R = N + mARow - mACol;
		int st = mARow + mACol;           // 구간의 시작
		int ed = mBRow + mBCol;           // 구간의 마지막
		if (st > ed) swap(st, ed);        // "구간의 시작 <= 구간의 마지막"이 되도록 하기
		rightRange[R] = { st, ed };       // 오른쪽 대각선(\) 길 구간정보 기록
		for (auto id : rightList[R]) {
			if (st <= hole[id].L && hole[id].L <= ed)
				rightRoad[R].push_back(id);
		}
	}
}

void addHole(int mRow, int mCol, int mID)
{
	hole[mID].init(mRow, mCol);                // 홀을 생성
	hori[mRow].push_back(mID);                 // 홀 아이디를 수평선에 추가
	vert[mCol].push_back(mID);                 // 홀 아이디를 수직선에 추가

	int L = mRow + mCol, R = N + mRow - mCol;
	leftList[L].push_back(mID);      // 홀 아이디를 왼쪽 대각선에 추가
	rightList[R].push_back(mID);     // 홀 아이디를 오른쪽 대각선에 추가
	if (leftRange[L].first <= R && R <= leftRange[L].second)
		leftRoad[L].push_back(mID);
	if (rightRange[R].first <= L && L <= rightRange[R].second)
		rightRoad[R].push_back(mID);
}

void eraseHole(int mRow, int mCol) // O(20 * 3000:FC)
{
	int id = -1;
	for (auto i : hori[mRow]) if (hole[i].c == mCol) { // 가로방향에서 홀 찾기
		id = i;  break; // 찾은 경우
	}
	if (id < 0) return; // 홀을 찾을 수 없는 경우
	hole[id].del = 1;   // 지웠다고 표시하기
}

int holeID, holeDist;   // 구슬이 이동하고자 하는 홀의 아이디와 거리
void search(vector<int>& vec) {
	for (auto id : vec) {
		if (hole[id].del) continue;    // 삭제된 경우는 제외
		if (hole[id].closed) continue; // 닫힌 홀은 제외
		int dist = hole[0] - hole[id]; // 구슬과 홀 사이 거리
		if (holeDist > dist) holeID = id, holeDist = dist;     // 1우선순위 : 거리
		else if (holeDist == dist && hole[id] < hole[holeID])  // 2우선순위 r, 3우선순위 c 
			holeID = id;
	}
}

int hitMarble(int mRow, int mCol, int mK)
{
	vector<int> closedHoleList;  // 닫힌 홀 목록
	int retID = -1;
	hole[0].init(mRow, mCol);    // 현재 구슬의 위치를 Hole객체로 생성
	auto& tg = hole[0];           // 구슬의 현재 위치
	for (int i = 0; i < mK; ++i) {
		int L = tg.r + tg.c, R = N + tg.r - tg.c; // 구슬의 대각선 번호
		holeID = -1, holeDist = 1 << 20;  // 구슬을 넣을 홀아이디와 거리
		search(hori[tg.r]);      // 가로방향 탐색
		search(vert[tg.c]);      // 세로방향 탐색
		if (leftRange[L].first <= R && R <= leftRange[L].second)
			search(leftRoad[L]);    // 왼쪽대각선(/)
		if (rightRange[R].first <= L && L <= rightRange[R].second)
			search(rightRoad[R]);// 오른쪽대각선(\)
		if (holeID < 0) break;   // 게임을 더이상 할 수 없는 경우
		closedHoleList.push_back(holeID); // 닫힌 홀 목록
		hole[holeID].closed = 1; // 홀 닫기
		tg = hole[holeID];       // 닫힌 홀 위에 구슬을 놓기
		retID = holeID;
	}
	for (auto id : closedHoleList)
		hole[id].closed = 0;     // 닫힌 홀을 다시 열기
	return retID;                // 마지막 홀 번호 반환
}
#endif
```
