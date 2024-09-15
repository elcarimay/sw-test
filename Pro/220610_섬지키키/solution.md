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

int map[20][20], cmap[20][20];
bool visit[20][20];
int N;
priority_queue<int> pq;

void init(int N, int mMap[20][20]) {
	::N = N;
	for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) 
		map[i][j] = mMap[i][j];
}

int numberOfCandidate(int M, int mStructure[5]) {
	if (M == 1) return N * N;
	int ret = 0, value = 0, value_rev = 0, rev = 0;
	for (int k = 0; k < M; k++)
		if (mStructure[k] == mStructure[M - 1 - k]) rev++;

	for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
		int h = 1, hr = 1, v = 1, vr = 1;
		for (int k = 0; k < M; k++) {
			if (k == 0) {
				h = hr = v = vr = 1;
				value = map[i][j] + mStructure[0]; value_rev = map[i][j] + mStructure[M - 1];
			}
			else {
				if (j < N - M + 1) {
					if (value == (map[i][j + k] + mStructure[k])) h++;
					if (rev != M && value_rev == (map[i][j + k] + mStructure[M - 1 - k])) hr++;
				}
				if (i < N - M + 1) {
					if (value == (map[i + k][j] + mStructure[k])) v++;
					if (rev != M && value_rev == (map[i + k][j] + mStructure[M - 1 - k])) vr++;
				}
			}
		}
		if (h == M) ret++; if (hr == M) ret++; if (v == M) ret++; if (vr == M) ret++;
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
	for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) cmap[i][j] = map[i][j];
	if (M == 1) {
		cmap[r][c] = map[r][c] + mSt[0]; return;
	}
	if (type == 1) for (int i = 0; i < M; i++) cmap[r][c + i] += mSt[i];
	else if (type == 2) for (int i = 0; i < M; i++) cmap[r][c + i] += mSt[M - 1 - i];
	else if (type == 3) for (int i = 0; i < M; i++) cmap[r + i][c] += mSt[i];
	else for (int i = 0; i < M; i++) cmap[r + i][c] += mSt[M - 1 - i];
}

void cal(int level) {
	memset(visit, 0, sizeof(visit));
	int total = 0;
	for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
		if (i != 0 && j != 0 && i != N - 1 && j != N - 1) continue;
		if (visit[i][j] || cmap[i][j] >= level) continue;
		total += bfs(i, j, level);
	}
	pq.push(N * N - total);
}

int maxArea(int M, int mStructure[5], int mSeaLevel) {
	while (!pq.empty()) pq.pop();
	if (M == 1) {
		for (int ii = 0; ii < N; ii++) for (int jj = 0; jj < N; jj++) {
			if (map[ii][jj] >= mSeaLevel) continue;
			memset(visit, 0, sizeof(visit));
			make_map(ii, jj, M, mStructure, 1);
			int total = 0;
			for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
				if (i != 0 && j != 0 && i != N - 1 && j != N - 1) continue;
				if (visit[i][j] || cmap[i][j] >= mSeaLevel) continue;
				total += bfs(i, j, mSeaLevel);
			}
			pq.push(N * N - total);
		}
		return pq.empty() ? -1 : pq.top();
	}
	int ret = 0, value = 0, value_rev = 0, rev = 0;
	for (int k = 0; k < M; k++)
		if (mStructure[k] == mStructure[M - 1 - k]) rev++;

	for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
		int h = 1, hr = 1, v = 1, vr = 1;
		for (int k = 0; k < M; k++) { // hor
			if (k == 0) {
				h = hr = v = vr = 1;
				value = map[i][j] + mStructure[0];
				value_rev = map[i][j] + mStructure[M - 1];
			}
			else {
				if (j < N - M + 1) {
					if (value == (map[i][j + k] + mStructure[k])) h++;
					if (rev != M && value_rev == (map[i][j + k] + mStructure[M - 1 - k])) hr++;
				}
				if (i < N - M + 1) {
					if (value == (map[i + k][j] + mStructure[k])) v++;
					if (rev != M && value_rev == (map[i + k][j] + mStructure[M - 1 - k])) vr++;
				}
			}
		}
		if (h == M) {
			make_map(i, j, M, mStructure, 1);
			cal(mSeaLevel);
		}
		if (hr == M) {
			make_map(i, j, M, mStructure, 2);
			cal(mSeaLevel);
		}
		if (v == M) {
			make_map(i, j, M, mStructure, 3);
			cal(mSeaLevel);
		}
		if (vr == M) {
			make_map(i, j, M, mStructure, 4);
			cal(mSeaLevel);
		}
	}
	return pq.empty() ? -1 : pq.top();
}
```
