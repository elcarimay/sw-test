```cpp
#if 1
/* 1. 맨처음 init에 모든 위치에서 땅특성 hash key생성
   2. bfs할때 visit에 방문해야할 땅들만 que에 넣고 진행
*/

#include <iostream>
#include <string.h>
#include <algorithm>
#include <vector>
#include <unordered_map>
#include <set>
#include <queue>
using namespace std;
using pii = pair<int, int>; // r, c

struct Data {
	int r, c, dir;
};

int map[20][20], cmap[20][20];
int N;
priority_queue<int> pq;
vector<Data> hmap[10000];

int getKey(vector<int>& v, bool rev = 0) {
	int key = 0;
	if (rev == 0)
		for (int i = 1; i < v.size(); i++)
			key = key * 10 + v[i] - v[i - 1] + 5;
	else
		for (int i = v.size() - 2; i >= 0; i--)
			key = key * 10 + v[i] - v[i + 1] + 5;
	return key;
}

void init(int N, int mMap[20][20]) {
	::N = N;
	for (auto& v : hmap) v.clear();
	for (int i = 0; i < N; i++) {
		for (int j = 0; j < N; j++) {
			map[i][j] = mMap[i][j];
			vector<int> v1, v2;
			int k1, k2;
			for (int k = j; k < j + 5 && k < N; k++) {
				v1.push_back(mMap[i][k]);							// (i,j)->(i,k)
				v2.push_back(mMap[k][i]);							// (j,i)->(k,i)

				k1 = getKey(v1), k2 = getKey(v1, 1);			// k1: 좌->우 , k2: 우->좌
				hmap[k1].push_back({ i,j,0 });
				if (j == k) continue;							// 크기 1인 경우, 한 방향만 등록
				if (k1 != k2) hmap[k2].push_back({ i,k,2 });	// 뒤집었을 때 다른 경우만 등록

				k1 = getKey(v2), k2 = getKey(v2, 1);			// k1: 상->하 , k2: 하->상
				hmap[k1].push_back({ j,i,1 });
				if (k1 != k2) hmap[k2].push_back({ k,i,3 });
			}
		}
	}
}

int getKey2(int M, int s[]) {					// 구조물을 설치할 수 있는 땅의 고도를 가장 작은 값 1로 표현
	int key = 0;
	for (int i = 1; i < M; i++)
		key = key * 10 + s[i - 1] - s[i] + 5;
	return key;
}

int numberOfCandidate(int M, int mStructure[5]) {
	int ret = hmap[getKey2(M, mStructure)].size();
	return ret;
}

int dr[] = { 0,1,0,-1 }, dc[] = { 1,0,-1,0 }; // 0: 우, 1: 하, 2: 좌, 3: 상

pii que[20 * 20 + 5];
int head, tail;
int bfs(int level) {
	bool visited[23][23] = {};
	head = tail = 0;
	int r = 0, c = 0;
	for (int i = 0; i < 4; i++) { // 외곽에만 돔.
		for (int j = 0; j < N - 1; j++) { // for문이 외곽만 돌게끔 값이 나옴
			r += dr[i], c += dc[i];
			if (map[r][c] < level) {
				que[tail++]={ r,c };
				visited[r][c]++;
			}
		}
	}
	int remain = N * N;
	while (head < tail) {
		int r = que[head].first;
		int c = que[head++].second;
		remain--;
		for (int i = 0; i < 4; i++) {
			int nr = r + dr[i], nc = c + dc[i];
			if (nr < 0 || nr >= N || nc < 0 || nc >= N) continue;
			if (visited[nr][nc] || (map[nr][nc] >= level)) continue;
			que[tail++] = { nr,nc };
			visited[nr][nc]++;
		}
	}
	return remain;
}

int maxArea(int M, int mStructure[5], int mSeaLevel) {
	int ret = -1;
	for (Data d : hmap[getKey2(M, mStructure)]) {
		for (int i = 0; i < M; i++)
			map[d.r + dr[d.dir] * i][d.c + dc[d.dir] * i] += mStructure[i];

		ret = max(ret, bfs(mSeaLevel));

		for (int i = 0; i < M; i++)
			map[d.r + dr[d.dir] * i][d.c + dc[d.dir] * i] -= mStructure[i];
	}
	return ret;
}
#endif // 0
```
