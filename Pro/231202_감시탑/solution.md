```cpp
#if 1
#include <vector>
#include <algorithm>
#include <string.h>
using namespace std;
struct Pos {
	int r, c;
	bool operator==(const Pos& pos)const {
		return r == pos.r && c == pos.c;
	}
};

#define SIZE 50 // 그룹크기, 50 * 50
int N; // 맵 범위
int M; // 그룹 범위 M = N / MAX_SIZE;
vector<Pos> v[105][105][6]; // v[group_row][group_col][color] = {row,col} 리스트

void init(int N){
	::N = N, M = N / SIZE;
	for (int i = 0; i <= M; i++)
		for (int j = 0; j <= M; j++)
			for (int k = 1; k <= 5; k++)
				v[i][j][k].clear();
}

void buildTower(int mRow, int mCol, int mColor){
	v[mRow / SIZE][mCol / SIZE][mColor].push_back({ mRow, mCol });
}

void removeTower(int mRow, int mCol){
	int R = mRow / SIZE, C = mCol / SIZE;
	for (int color = 1; color <= 5; color++) {
		auto& V = v[R][C][color];
		auto it = find(V.begin(), V.end(), Pos{ mRow, mCol });
		if (it != V.end()) {
			V.erase(it); break;
		}
	}
}

int countTower(int mRow, int mCol, int mColor, int mDis){
	int res = 0;
	int R = mRow / SIZE, C = mCol / SIZE;
	int sR = max(0, R - 1), sC = max(0, C - 1);
	int eR = min(M, R + 1), eC = min(M, C + 1);
	for (int i = sR; i <= eR; i++)
		for (int j = sC; j <= eC; j++)
			for (int k = 1; k <= 5; k++) {
				if (mColor && mColor != k) continue;
				for (auto& nx : v[i][j][k]) {
					if (abs(nx.r - mRow) > mDis) continue;
					if (abs(nx.c - mCol) > mDis) continue;
					res++;
				}
			}
	return res;
}

// (R, C) 그룹의 color 색 감시탑 최소거리 갱신
int row, col, color, minDist;
void setMinDist(int R, int C) {
	for (int i = 1; i < 5; i++) {
		if (color && color != i) continue;
		for (auto& nx : v[R][C][i]) {
			int dist = abs(nx.r - row) + abs(nx.c - col);
			minDist = min(minDist, dist);
		}
	}
}

int dr[] = { 1,1,-1,-1 }, dc[] = { 1,-1,-1,1 };
int getClosest(int mRow, int mCol, int mColor){
	row = mRow, col = mCol, color = mColor;
	int R = mRow / SIZE, C = mCol / SIZE;
	int maxD = max(R, M - R) + max(C, M - C);
	minDist = 1e9;
	setMinDist(R, C);
	for (int D = 1; D <= maxD; D++) {
		if (minDist <= (D - 2) * SIZE + 2) break;

		int curR = R - D, curC = C;
		for (int j = 0; j < 4; j++)
			for (int k = 0; k < D; k++) {
				curR += dr[j], curC += dc[j];
				if (curR <0 || curR > M || curC < 0 || curC > M) continue;
				setMinDist(curR, curC);
			}
	}
	return minDist == 1e9 ? -1 : minDist;
}
#endif
```
