```cpp
#if 1 // made by kdh → 438 ms
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
using namespace std;
#define MAX_N 515
#define INF 987654321

bool map[MAX_N][MAX_N];
int idx; char head;
void decompressed(int r, int c, int size, char code[]) {
	head = code[idx++];
	while (head == ')') head = code[idx++];
	if (head == '0' || head == '1') {
		for (int dr = 0; dr < size; dr++)
			for (int dc = 0; dc < size; dc++)
				map[r + dr][c + dc] = int(head - '0');
	}
	else {
		int half = size / 2;
		decompressed(r, c, half, code);
		decompressed(r, c + half, half, code);
		decompressed(r + half, c, half, code);
		decompressed(r + half, c + half, half, code);
	}
}

int tempCnt;
void compress(int r, int c, int size, char temp[]) {
	int cnt = 0;
	for (int i = r; i < r + size; i++)
		for (int j = c; j < c + size; j++)
			cnt += map[i][j];
	if (cnt == 0) temp[tempCnt++] = '0';
	else if (cnt == size * size) temp[tempCnt++] = '1';
	else {
		int half = size / 2;
		temp[tempCnt++] = '(';
		compress(r, c, half, temp);
		compress(r, c + half, half, temp);
		compress(r + half, c, half, temp);
		compress(r + half, c + half, half, temp);
		temp[tempCnt++] = ')';
	}
}

struct Pos {
	int r, c, d;
}que[MAX_N * MAX_N];
bool visit[MAX_N][MAX_N];
int dr[] = { 0,-1,0,1 }, dc[] = { 1,0,-1,0 };
int N;
void bfs(int r, int c, int d, int color) {
	int head = 0, tail = 0;
	memset(visit, 0, sizeof(visit));
	map[r][c] = color, visit[r][c] = 1;
	que[tail].r = r, que[tail].c = c, que[tail++].d = 1;
	while (head < tail) {
		auto cur = que[head++];
		if (cur.d + 1 > d) break;
		for (int i = 0; i < 4; i++) {
			int nr = cur.r + dr[i];
			int nc = cur.c + dc[i];
			if (nr < 0 || nr > N - 1 || nc < 0 || nc > N - 1) continue;
			if (d == INF && map[nr][nc] == color) continue;
			if (!visit[nr][nc]) {
				que[tail].r = nr, que[tail].c = nc, que[tail++].d = cur.d + 1;
				map[nr][nc] = color, visit[nr][nc] = 1;
			}
		}
	}
}

void init(int N, int L, char mCode[]){
	::N = N, idx = 0;
	memset(map, 0, sizeof(map));
	decompressed(0, 0, N, mCode);
}

int encode(char mCode[]){
	tempCnt = 0;
	compress(0, 0, N, mCode);
	return tempCnt;
}

void makeDot(int mR, int mC, int mSize, int mColor){
	bfs(mR, mC, mSize, mColor);
	if (0) {
		for (int i = max(0, mR - mSize + 1); i < min(N, mR + mSize); i++)
			for (int j = max(0, mC - mSize + 1); j < min(N, mC + mSize); j++)
				if (abs(i - mR) + abs(j - mC) < mSize) map[i][j] = mColor;
	}
}

void paint(int mR, int mC, int mColor){
	if (map[mR][mC] == mColor) return;
	bfs(mR, mC, INF, mColor);
}

int getColor(int mR, int mC){
	return map[mR][mC];
}
#endif
```
