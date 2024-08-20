```cpp
#if 1
/*
* 블록 표현방식1 , BFS
*
* 2차원 배열로 왼쪽위에 밀착해서 표현
*
* oo    => 1,1,0,0,0 => {{1,1},{1,1}}
* oo       1,1,0,0,0
*          0,0,0,0,0
*          0,0,0,0,0
*          0,0,0,0,0
*
* oooo    =>  1,1,1,1,0 => {{1,1,1,1}}
*             0,0,0,0,0
*             0,0,0,0,0
*             0,0,0,0,0
*             0,0,0,0,0
*
* 반시계 회전, 좌우반전 시에도 왼쪽,위로 밀착
*/

#include <string.h>
#include <algorithm>
#include <unordered_map>
using namespace std;

#define MAXN 1000
int n, (*B)[MAXN], * tcnt, * pcnt;

unordered_map<int, int> hmap;

int block[18][5][5] = {
{{1, 1},{1, 1}},
{{1, 1, 1, 1}},
{{0, 1, 1},{1, 1}},
{{1},{1},{1,1}},
{{0,1},{1,1,1}},
{{0,1,1},{1,1},{0,1}},
{{1, 1, 1, 1, 1}},
{{1},{1},{1},{1,1}},
{{0,1},{1,1},{1},{1}},
{{1,1},{1,1},{1}},
{{1,1,1},{0,1},{0,1}},
{{1,0,1},{1,1,1}},
{{1},{1},{1,1,1}},
{{1},{1,1},{0,1,1}},
{{0,1},{1,1,1},{0,1}},
{{0,1},{1,1},{0,1},{0,1}},
{{1,1},{0,1},{0,1,1}}
};

int gethash(int id) {
	int hash = 0;
	for (int i = 0; i < 5; i++) for (int j = 0; j < 5; j++)
		hash = hash * 2 + block[id][i][j]; // 2진법 사용
	return hash;
}

void move(int id) { // 왼쪽 위 밀착
	int minx = 5, miny = 5;
	for (int i = 0; i < 5; i++) for (int j = 0; j < 5; j++)
		if (block[id][i][j]) minx = min(minx, i), miny = min(miny, j);
	if (minx == 0 && miny == 0) return;
	for (int i = minx; i < 5; i++) for (int j = miny; j < 5; j++) {
		block[id][i - minx][j - miny] = block[id][i][j];
		block[id][i][j] = 0;
	}
}

void rotate(int id) { // 반시계 90도
	int tmp[5][5];
	memcpy(tmp, block[id], sizeof(tmp));
	for (int i = 0; i < 5; i++) for (int j = 0; j < 5; j++)
		block[id][4 - j][i] = tmp[i][j];
	move(id);
}

void flip(int id) { // 좌우반전
	for (int i = 0; i < 5; i++) for (int j = 0; j < 2; j++)
		swap(block[id][i][j], block[id][i][4 - j]);
	move(id);
}

void init() {
	for (int id = 0; id < 17; id++) {
		for (int i = 0; i < 2; i++) {
			for (int j = 0; j < 4; j++) {
				hmap[gethash(id)] = id;
				rotate(id);
			}
			flip(id);
		}
	}
}

int q[5], head, tail, dx[] = { 1, 0, -1, 0 }, dy[] = { 0,1,0,-1 };
void bfs(int x, int y) {
	head = tail = 0;
	int minx = n, miny = n;
	int target = B[x][y];
	q[tail++] = x * n + y;
	B[x][y] = 0;
	while (head < tail) {
		int val = q[head++];
		minx = min(minx, val / n), miny = min(miny, val % n);
		for (int i = 0; i < 4; i++) {
			x = (val / n) + dx[i], y = (val % n) + dy[i];
			if (x < 0 || x >= n || y < 0 || y >= n) continue;
			if (B[x][y] != target) continue;
			q[tail++] = x * n + y;
			B[x][y] = 0;
		}
	}

	memset(block[17], 0, sizeof(block[17])); // 빈행렬 생성
	for (int i = 0; i < tail; i++) block[17][q[i] / n - minx][q[i] % n - miny] = 1;
	int id = hmap[gethash(17)];
	tail == 4 ? tcnt[id]++ : pcnt[id - 5]++;
}

void countBlock(int N, int mBoard[MAXN][MAXN], int mTetromino[5], int mPentomino[12]) {
	if (hmap.empty()) init();
	n = N;
	B = mBoard;
	tcnt = mTetromino, pcnt = mPentomino;
	for (int i = 0; i < N; i++) for (int j = 0; j < N; j++)
		if (B[i][j]) bfs(i, j);
}
#endif // 1

```
