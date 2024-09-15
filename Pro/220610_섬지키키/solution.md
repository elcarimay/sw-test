```cpp
#include <iostream>
#include <string.h>
#include <algorithm>
#include <vector>
#include <unordered_map>
#include <set>
#include <queue>
using namespace std;
using pii = pair<int, int>; // r, c

int map[20][20];
int cmap[20][20];
bool visit[20][20];
int N;
priority_queue<int> pq;

void init(int N, int mMap[20][20]){
	::N = N;
	for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
		map[i][j] = mMap[i][j];
	}
}

int numberOfCandidate(int M, int mStructure[5]){
	if (M == 1) return N * N;
	int ret = 0, value = 0;
	for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
		if (i == 4 && j == 2) {
			i = i;
		}
		int same = 1, sym = 0;
		for (int k = 0; k < M; k++) { // hor
			if (k == 0) {
				if (j + k == N) break;
				value = map[i][j + k] + mStructure[k];
			}
			else if(value == (map[i][j + k] + mStructure[k])) same++;
			if (mStructure[k] == mStructure[M - 1 - k]) sym++;
		}
		if (same == M) ret++;
		same = 1;
		if (sym != M) {
			for (int k = 0; k < M; k++) { // hor,reverse
				if (j + k == N) break;
				if (k == 0) {
					value = map[i][j + k] + mStructure[M - 1 - k];
				}
				else if (value == (map[i][j + k] + mStructure[M - 1 - k])) same++;
			}
			if (same == M) ret++;
		}
		same = 1;
		for (int k = 0; k < M; k++) { // ver
			if (i + k == N) break;
			if (k == 0) {
				value = map[i + k][j] + mStructure[k];
			}
			else if (value == (map[i + k][j] + mStructure[k])) same++;
		}
		if (same == M) ret++;
		same = 1;
		if (sym != M) {
			for (int k = 0; k < M; k++) { // ver,reverse
				if (i + k == N) break;
				if (k == 0) {
					value = map[i + k][j] + mStructure[M - 1 - k];
				}
				else if (value == (map[i + k][j] + mStructure[M - 1 - k])) same++;
			}
			if (same == M) ret++;
		}
	}
	return ret;
}

int dr[] = { 0,-1,0,1 }, dc[] = { 1,0,-1,0 };
pii que[20 * 20 + 5];
int head, tail;
int bfs(int r, int c, int level) {
	head = tail = 0;
	que[tail++] = { r,c };
	visit[r][c] = 1;
	int ret = 1;
	while (head < tail) {
		pii cur = que[head++];
		int r = cur.first, c = cur.second;
		for (int i = 0; i < 4; i++) {
			int nr = r + dr[i], nc = c + dc[i];
			if (nr < 0 || nr >= N || nc < 0 || nc >= N) continue;
			if (visit[nr][nc] || cmap[nr][nc] >= level) continue;
			que[tail++] = { nr,nc };
			visit[nr][nc]++;
			ret++;
		}
	}
	return ret;
}

void make_map(int r, int c, int M, int mSt[], int type) { // type 1:hor,2:hor_reverse,3:ver,4:ver_reverse
	if (r == 0 && c == 2) {
		r = r;
	}
	for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
		cmap[i][j] = map[i][j];
	}
	if (M == 1) {
		cmap[r][c] = map[r][c] + mSt[0]; return;
	}
	if (type == 1) for (int i = 0; i < M; i++) cmap[r][c + i] += mSt[i];
	else if (type == 2) for (int i = 0; i < M; i++) cmap[r][c + i] += mSt[M - 1 - i];
	else if (type == 3) for (int i = 0; i < M; i++) cmap[r + i][c] += mSt[i];
	else for (int i = 0; i < M; i++) cmap[r + i][c] += mSt[M - 1 - i];
}
int total = 0;
int maxArea(int M, int mStructure[5], int mSeaLevel){
	while (!pq.empty()) pq.pop();
	if (M == 1) {
		for (int ii = 0; ii < N; ii++) for (int jj = 0; jj < N; jj++) {
			if (map[ii][jj] >= mSeaLevel) continue;
			memset(visit, 0, sizeof(visit));
			make_map(ii, jj, M, mStructure, 1);
			total = 0;
			for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
				if (i != 0 && j != 0 && i != N - 1 && j != N - 1) continue;
				if (visit[i][j] || cmap[i][j] >= mSeaLevel) continue;
				total += bfs(i, j, mSeaLevel);
			}
			pq.push(N*N - total);
		}
		return pq.top();
	}
	int value = 0;
	for (int iii = 0; iii < N; iii++) for (int jjj = 0; jjj < N; jjj++) {
		int same = 1, sym = 0;
		for (int k = 0; k < M; k++) { // hor
			if (k == 0) {
				if (jjj + k == N) break;
				value = map[iii][jjj + k] + mStructure[k];
			}
			else if (value == (map[iii][jjj + k] + mStructure[k])) same++;
			if (mStructure[k] == mStructure[M - 1 - k]) sym++;
		}
		if (same == M) {
			make_map(iii, jjj, M, mStructure, 1);
			memset(visit, 0, sizeof(visit));
			total = 0;
			for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
				if (i != 0 && j != 0 && i != N - 1 && j != N - 1) continue;
				if (visit[i][j] || cmap[i][j] >= mSeaLevel) continue;
				total += bfs(i, j, mSeaLevel);
			}
			pq.push(N * N - total);
		}
		same = 1;
		if (sym != M) {
			for (int k = 0; k < M; k++) { // hor, reverse
				if (jjj + k == N) break;
				if (k == 0) {
					value = map[iii][jjj + k] + mStructure[M - 1 - k];
				}
				else if (value == (map[iii][jjj + k] + mStructure[M - 1 - k])) same++;
			}
			if (same == M) {
				make_map(iii, jjj, M, mStructure, 2);
				memset(visit, 0, sizeof(visit));
				total = 0;
				for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
					if (i != 0 && j != 0 && i != N - 1 && j != N - 1) continue;
					if (visit[i][j] || cmap[i][j] >= mSeaLevel) continue;
					total += bfs(i, j, mSeaLevel);
				}
				pq.push(N * N - total);
			}
		}
		
		same = 1;
		for (int k = 0; k < M; k++) { // ver
			if (iii + k == N) break;
			if (k == 0) {
				value = map[iii + k][jjj] + mStructure[k];
			}
			else if (value == (map[iii + k][jjj] + mStructure[k])) same++;
		}
		if (same == M) {
			make_map(iii, jjj, M, mStructure, 3);
			memset(visit, 0, sizeof(visit));
			total = 0;
			for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
				if (i != 0 && j != 0 && i != N - 1 && j != N - 1) continue;
				if (visit[i][j] || cmap[i][j] >= mSeaLevel) continue;
				total += bfs(i, j, mSeaLevel);
			}
			pq.push(N * N - total);
		}
		same = 1;
		if (sym != M) {
			for (int k = 0; k < M; k++) { // ver, reverse
				if (iii + k == N) break;
				if (k == 0) {
					value = map[iii + k][jjj] + mStructure[M - 1 - k];
				}
				else if (value == (map[iii + k][jjj] + mStructure[M - 1 - k])) same++;
			}
			if (same == M) {
				make_map(iii, jjj, M, mStructure, 4);
				memset(visit, 0, sizeof(visit));
				total = 0;
				for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
					if (i != 0 && j != 0 && i != N - 1 && j != N - 1) continue;
					if (visit[i][j] || cmap[i][j] >= mSeaLevel) continue;
					total += bfs(i, j, mSeaLevel);
				}
				pq.push(N* N - total);
			}
		}
	}
	return pq.empty() ? -1: pq.top();
}
```
