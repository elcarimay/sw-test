```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <string.h>
#include <string>
#include <algorithm>
using namespace std;
#define MAX_N 16

int map[MAX_N][MAX_N];
string s;

void decompressed(string::iterator& it, int r, int c, int size) {
	char head = *(it++);
	if (head == '0' || head == '1') {
		for (int dr = 0; dr < size; dr++)
			for (int dc = 0; dc < size; dc++)
				map[r + dr][c + dc] = head - '0';
	}
	else {
		int half = size / 2;
		decompressed(it, r, c, half);
		decompressed(it, r, c + half, half);
		decompressed(it, r + half, c, half);
		decompressed(it, r + half, c + half, half);
	}
}

void compress(int a[MAX_N][MAX_N], int r, int c, int size, string& temp) {
	int cnt = 0;
	for (int i = r; i < r + size; i++)
		for (int j = c; j < c + size; j++)
			cnt += a[i][j];
	if (cnt == 0) temp += "0";
	else if (cnt == size * size) temp += "1";
	else {
		int half = size / 2;
		temp += "(";
		compress(a, r, c, half, temp);
		compress(a, r, c + half, half, temp);
		compress(a, r + half, c, half, temp);
		compress(a, r + half, c + half, half, temp);
		temp += ")";
	}

}

struct Pos {
	int r, c, d;
}que[MAX_N * MAX_N];
bool visit[MAX_N][MAX_N];
int R = MAX_N, C = MAX_N;
int dr[] = { 0,-1,0,1 }, dc[] = { 1,0,-1,0 };
void bfs(int r, int c, int d) {
	int head = 0, tail = 0;
	memset(visit, 0, sizeof(visit));
	visit[r][c] = 0;
	que[tail].r = r, que[tail].c = c, que[tail++].d = 0;
	while (head < tail) {
		auto cur = que[head++];
		if (cur.d > d) break;
		for (int i = 0; i < 4; i++) {
			int nr = cur.r + dr[i];
			int nc = cur.c + dc[i];
			if (nr < 0 || nr > R || nc < 0 || nc > C) continue;
			if (!map[nr][nc] && !visit[nr][nc]) {
				visit[nr][nc] = 1;
				que[tail].r = nr, que[tail].c = nc, que[tail++].d++;
				map[nr][nc] = 1;
			}
		}
	}
}

int main() {
	memset(map, 0, sizeof(map));
	if (1) {
		//char c[] = "((1010)(1110)(1011)(1111))";
		char c[] = "(1010)";
		s += "x";
		for (int i = 1; i < strlen(c); i++) {
			if (c[i] == '(') s += "x";
			else if (c[i] == ')') continue;
			else s += c[i];
		}

		auto it = s.begin();
		decompressed(it, 0, 0, MAX_N);
	}

	if (0) {
		string temp;
		char ch[] = "xx0001x0x011100xxx00111000011";
		temp = string(ch);
		auto it = temp.begin();
		decompressed(it, 0, 0, MAX_N);
	}

	s = {};
	compress(map, 0, 0, MAX_N, s);
	bfs(1, 1, 1);
	return 0;
}
#endif
```
