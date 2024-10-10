```cpp
struct Result {
	int id, num;
};

#include <algorithm>
#include <string.h>
#include <queue>
using namespace std;
using pii = pair<int, int>;

int N, mid, tid; // 극장수, 예약번호, 극장번호
int maxCnt[2003]; // 극장별 최대묶음개수
int reserved[2003][10][10]; // 극장별 예약상황
int tID[50003]; // 예약번호별 극장
priority_queue<int, vector<int>, greater<>> pq;

void init(int N) {
	::N = N;
	fill(maxCnt + 1, maxCnt + 1 + N, 100);
	memset(reserved, 0, sizeof(reserved));
	tid = 1;
}

bool visited[10][10];
int dr[] = { -1,0,1,0 }, dc[] = { 0,1,0,-1 };

pii que[101];
int head, tail;
int getBundleCnt(int r, int c) { // bfs
	head = tail = 0;
	int cnt = 1;
	visited[r][c] = 1;
	que[tail++] = { r, c };
	while (head < tail) {
		pii cur = que[head++];
		for (int i = 0; i < 4; i++) {
			int nr = cur.first + dr[i], nc = cur.second + dc[i];
			if (nr < 0 || nr >= 10 || nc < 0 || nc >= 10) continue;
			if (visited[nr][nc]) continue;
			if (reserved[tid][nr][nc]) continue;
			visited[nr][nc] = 1;
			que[tail++] = { nr, nc };
			cnt++;
		}
	}
	return cnt;
}

int getFirstSeat(int k) {
	memset(visited, 0, sizeof(visited));
	for (int i = 0; i < 10; i++)
		for (int j = 0; j < 10; j++)
			if (!visited[i][j] && !reserved[tid][i][j]) {
				int num = getBundleCnt(i, j);
				if (k <= num)
					return i * 10 + j + 1;
			}
}

void bfs(int rc, int cnt) {
	memset(visited, 0, sizeof(visited));
	pq = {}; pq.push(rc);
	visited[rc / 10][rc % 10] = 1;
	while (cnt--) {
		int cur = pq.top(); pq.pop();
		int r = cur / 10, c = cur % 10;
		reserved[tid][r][c] = mid;
		for (int i = 0; i < 4; i++) {
			int nr = r + dr[i], nc = c + dc[i];
			if (nr < 0 || nr >= 10 || nc < 0 || nc >= 10) continue;
			if (visited[nr][nc]) continue;
			if (reserved[tid][nr][nc]) continue;
			pq.push(nr * 10 + nc);
			visited[nr][nc] = 1;
		}
	}
}

void setMaxCnt() {
	memset(visited, 0, sizeof(visited));
	maxCnt[tid] = 0;
	for (int i = 0; i < 10; i++)
		for (int j = 0; j < 10; j++)
			if (!visited[i][j] && !reserved[tid][i][j])
				maxCnt[tid] = max(maxCnt[tid], getBundleCnt(i, j));
}

Result reserveSeats(int mID, int K){
	Result res = { 0,0 };
	mid = mID;
	tid = 1;
	while (maxCnt[tid] < K && tid <= N) tid++;
	if (tid == N + 1) return res;
	int rc = getFirstSeat(K); // 시작 좌석지점
	res = { tid, rc };
	tID[mID] = tid;
	bfs(rc - 1, K);
	setMaxCnt();
	return res;
}

Result cancelReservation(int mID){
	Result res = { 0,0 };
	mid = mID; tid = tID[mID];
	res = { tid, 0 };

	for(int i = 0; i < 10; i++)
		for (int j = 0; j < 10; j++)
			if (reserved[tid][i][j] == mID) {
				reserved[tid][i][j] = 0;
				res.num += i * 10 + j + 1;
			}
	setMaxCnt();
	return res;
}
```
