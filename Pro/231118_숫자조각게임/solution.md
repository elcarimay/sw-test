```cpp
#if 1
#define MAX_ROW 40
#define MAX_COL 30
#include <cstdio>
#include <queue>
#include <string.h>
#include <vector>
#include <algorithm>
#include <unordered_map>
using namespace std;

struct Result {
	int row;
	int col;
};

int frames[5][3][3] = {         // 조각 종류에 따른 틀
	{{1,1,0},{0,0,0},{0,0,0}},  // type0 = 110 000 000
	{{1,1,1},{0,0,0},{0,0,0}},  // type1 = 111 000 000
	{{1},{1},{1}},              // type2 = 100 100 100
	{{1,1},{0,1,1}},            // type3 = 110 011 000
	{{1},{1,1,1},{0,0,1}}       // type4 = 100 111 001
};

int R, C;
int(*map)[MAX_COL];                       // 게임판

unordered_map<int, vector<Result>> hmap;   // key => 놓을수있는 좌표 리스트
bool used[40][30];                      // 놓여진 조각 표시

/*
* (sr,sc) 에 frame틀 놓을 수 있는지 판별
* 놓을 수 있으면 puzzle에 틀 정보만 기록하고 return 1
* 놓을 수 없으면 return 0
* (놓을 수 없는 경우는 게임판 범위 벗어나는 경우)
*
* frame    A      puzzle
* 110      342    340
* 011   => 322 => 022
* 000      345    000
*/
int puzzle[3][3];
bool setPuzzle(int sr, int sc, int(*frame)[3]) {
	for (int i = 0; i < 3; i++) {
		for (int j = 0; j < 3; j++) {
			puzzle[i][j] = 0;
			if (frame[i][j]) { // frame이 게임판 밖으로 나갔는지 판별
				if (sr + i >= R || sc + j >= C) return false;
				puzzle[i][j] = map[sr + i][sc + j];
			}
		}
	}
	return true;
}
/*
* key값 생성 반환
* 0이 아닌 위치의 최소값을 1로 만들고 10진법 (6진법 이상이면 됨)
*
* 340    120
* 035 => 013 => 120013000
* 000    000
*/
int getKey(int(*puzzle)[3]) {
	int minVal = 5;
	for (int i = 0; i < 3; i++) for (int j = 0; j < 3; j++)
		if (puzzle[i][j] && minVal > puzzle[i][j]) minVal = puzzle[i][j];

	int key = 0;
	for (int i = 0; i < 3; i++) for (int j = 0; j < 3; j++) {
		key *= 10;
		if (puzzle[i][j]) key += puzzle[i][j] - minVal + 1;
	}
	return key;
}

void init(int mRows, int mCols, int mCells[MAX_ROW][MAX_COL])
{
	hmap.clear();
	memset(used, 0, sizeof(used));
	R = mRows, C = mCols, map = mCells;

	for (int i = 0; i < R; i++) {               // sr
		for (int j = 0; j < C; j++) {           // sc
			for (int k = 0; k < 5; k++) {       // type
				if (setPuzzle(i, j, frames[k]) == false) continue;
				hmap[getKey(puzzle)].push_back({ i,j });
			}
		}
	}
}
/*
* 놓고자 하는 위치에 이미 놓여져 있으면 return 0
* 놓을 수 있으면 놓고 return 1
*/
bool putIfPossible(int(*puzzle)[3], int sr, int sc) {
	// false로 지정된 위치를 넘겼을때 다음에서 true로 할수 있기 때문에 
	// 아래와 같이 2개로 진행해야됨
	for (int i = 0; i < 3; i++)
		for (int j = 0; j < 3; j++)
			if (puzzle[i][j] && used[sr + i][sc + j]) return false;

	for (int i = 0; i < 3; i++)
		for (int j = 0; j < 3; j++)
			if (puzzle[i][j]) used[sr + i][sc + j] = true;

	return true;
}

Result putPuzzle(int mPuzzle[3][3])
{
	auto& v = hmap[getKey(mPuzzle)];
	for (auto p : v) { // 최대 500, 평균 70
		int r = p.row, c = p.col;
		if (putIfPossible(mPuzzle, r, c)) return { r,c };
	}
	return { -1, -1 };
}

void clearPuzzles()
{
	memset(used, 0, sizeof(used));
}
#endif
```
