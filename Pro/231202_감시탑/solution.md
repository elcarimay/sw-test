```cpp
#if 1
#include <vector>
using namespace std;

#define MAXRC 103
#define MAXCOL 6
#define SIZE 50 // bucket size

struct Pos {
	int r, c;
};

vector<Pos> p[MAXRC][MAXRC][MAXCOL];

int M;
void init(int N) {
	M = N / SIZE;
	for (int i = 0; i <= M; i++) for (int j = 0; j <= M; j++) for (int k = 1; k <= 5; k++)
		p[i][j][k].clear();
}

void buildTower(int mRow, int mCol, int mColor) {
	p[mRow / SIZE][mCol / SIZE][mColor].push_back({ mRow, mCol });
}

void removeTower(int mRow, int mCol) {
	for (int i = 1; i <= 5; i++) {
		auto& cur = p[mRow / SIZE][mCol / SIZE][i];
		for (int j = 0; j < cur.size(); j++) {
			if (mRow == cur[j].r && mCol == cur[j].c) {
				cur.erase(cur.begin() + j); return;
			}
		}
	}
}

int countTower(int mRow, int mCol, int mColor, int mDis) { // 좌우 8개만 조사하면 됨(mDis최대가 50이므로)
	int sr = max(0, mRow / SIZE - 1), sc = max(0, mCol / SIZE - 1);
	int er = min(mRow / SIZE + 1, M), ec = min(mCol / SIZE + 1, M);
	int ret = 0;
	for (int r = sr; r <= er; r++) for (int c = sc; c <= ec; c++)
		for (int i = 1; i <= 5; i++)
			for (Pos nx : p[r][c][i]) {
				if (abs(mRow - nx.r) > mDis) continue;
				if (abs(mCol - nx.c) > mDis) continue;
				if (mColor == 0) ret++;
				else if (i == mColor) ret++;
			}
	return ret;
}

int dr[] = { 1,1,-1,-1 }, dc[] = { 1,-1,-1,1 };
int minDist;
void setMinDist(int R, int C, int mRow, int mCol, int mColor) {
	for (int i = 1; i <= 5; i++) {
		if (mColor != 0 && mColor != i) continue;
		for (auto& nx : p[R][C][i]) {
			int dist = abs(nx.r - mRow) + abs(nx.c - mCol);
			minDist = min(minDist, dist);
		}
	}
}

int getClosest(int mRow, int mCol, int mColor) {
	int R = mRow / SIZE, C = mCol / SIZE;
	int maxD = max(R, M - R) + max(C, M - C);
	minDist = INT_MAX;
	setMinDist(R, C, mRow, mCol, mColor);
	for (int D = 1; D <= maxD; D++) {
		if (minDist <= (D - 2) * SIZE + 2) break;

		int curR = R - D, curC = C;
		for (int j = 0; j < 4; j++)
			for (int k = 0; k < D; k++) {
				curR += dr[j], curC += dc[j];
				if (curR < 0 || curR > M || curC < 0 || curC > M) continue;
				setMinDist(curR, curC, mRow, mCol, mColor);
			}
	}
	return minDist == INT_MAX ? -1 : minDist;
}
#endif
```
