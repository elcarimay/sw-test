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
int distMap[MAXRC][MAXRC];
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

int minDist, partitionDist, minpartitionDist;
void getMinDist(int r, int c, int partR, int partC, int mColor) {
	for (int i = 1; i <= 5; i++) {
		if (mColor != 0 && mColor != i) continue;
		for (auto& nx : p[partR][partC][i]) {
			int dist = abs(nx.r - r) + abs(nx.c - c);
			if (minDist > dist) {
				minDist = dist;
				minpartitionDist = partitionDist;
			}
		}
	}
}

int dr[] = { 0,-1,0,1 }, dc[] = { 1,0,-1,0 };
Pos que[10003]; int head, tail;
int getClosest(int mRow, int mCol, int mColor) {
	int R = mRow / SIZE, C = mCol / SIZE;
	for (int i = 0; i <= M; i++) for (int j = 0; j <= M; j++) distMap[i][j] = INT_MAX;
	distMap[R][C] = head = tail = 0;
	minDist = INT_MAX;
	minpartitionDist = -1;
	que[tail++] = { R,C };
	while (head < tail) {
		Pos cur = que[head++];
		partitionDist = distMap[cur.r][cur.c];
		getMinDist(mRow, mCol, cur.r, cur.c, mColor);
		if (minDist != INT_MAX && partitionDist - minpartitionDist > 2) break; // 현재 위치의 그룹거리가 최소로 찾은 그룹거리의 2보다 크면 종료
		for (int i = 0; i < 4; i++) {
			int nr = cur.r + dr[i], nc = cur.c + dc[i];
			if (nr < 0 || nr > M || nc < 0 || nc > M) continue;
			if (distMap[nr][nc] <= partitionDist + 1) continue;
			distMap[nr][nc] = partitionDist + 1;
			que[tail++] = { nr, nc };
		}
	}
	return minDist == INT_MAX ? -1 : minDist;
}
#endif
```
