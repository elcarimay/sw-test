```cpp
#include <unordered_map>
#include <string.h>
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;
using pii = pair<int, int>;

#define MAX_SIZE	1000
int N;
bool isPossible(int sr, int sc, int map[MAX_SIZE][MAX_SIZE]) {
	int n1 = 0, n2 = 0, n3 = 0, n4 = 0, nt = 0;
	for (int r = 0; r < 5; r++)	for (int c = 0; c < 5; c++) {
		if (r == 0) n1 += map[sr + r][sc + c];
		if (r == 6) n3 += map[sr + r][sc + c];
		if (c == 0) n2 += map[sr + r][sc + c];
		if (c == 6) n4 += map[sr + r][sc + c];
		nt += map[sr + r][sc + c];
	}
	if (!(n1 && n2 && n3 && n4 && nt == 7)) return false;
	return true;
}

int getHash(int piece[5][5]) {
	int temp[4] = { 0,0,0,0 };
	for (int i = 0; i < 5; i++) {
		for (int j = 0; j < 5; j++) {
			temp[0] = temp[0] * 2 + piece[i][j];
			temp[1] = temp[1] * 2 + piece[j][4 - i];
			temp[2] = temp[2] * 2 + piece[4 - i][4 - j];
			temp[3] = temp[3] * 2 + piece[4 - j][i];
		}
	}

	int minHashKey = temp[0];
	for (int i = 1; i < 4; i++) minHashKey = min(minHashKey, temp[i]);
	return minHashKey;
}
int(*map)[MAX_SIZE];
unordered_map <int, pii> constellation;
void init(int N, int mPlane[MAX_SIZE][MAX_SIZE]){
	map = mPlane;
	::N = N; constellation.clear();
	int temp[5][5];
	int startCnt;
	for (int i = 0; i <= N - 5; i++) for (int j = 0; j < N; j++) {
		if (!isPossible(i, j, mPlane)) continue;
		int value = getHash(i, j);
	}
}

int getCount(int mPiece[5][5]){

	return 0;
}

int getPosition(int mRow, int mCol){

	return 0;
}
```
