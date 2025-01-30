```cpp
#include <iostream>
using namespace std;

#define MAXN 30003
int seat[MAXN + 1], p[8][MAXN + 1], r[8][MAXN + 1], cand[8][MAXN + 1];
int W, H;

int calcDist(int a, int b){
	int dr = ((a - 1) / W) - ((b - 1) / W);
	int dc = ((a - 1) % W) - ((b - 1) % W);
	return max(abs(dr), abs(dc));
}

void initial(int n) {
	for (int i = 1; i <= n; i++) {
		seat[i] = 0;
		for (int d = 0; d < 8; d++)
			p[d][i] = i, r[d][i] = 0, cand[d][i] = i;
	}
}

int find(int d, int seatNum) {
	if (p[d][seatNum] == seatNum) return seatNum;
	return p[d][seatNum] = find(d, p[d][seatNum]);
}

void Union(int d, int a, int b) {
	a = find(d, a), b = find(d, b);
	if (a == b) return;
	int ca = d < 4 ? max(cand[d][a], cand[d][b]) : min(cand[d][a], cand[d][b]);
	if (r[d][a]< r[d][b])
		p[d][a] = b, cand[d][b] = ca;
	else if (r[d][a]> r[d][b])
		p[d][b] = a, cand[d][a] = ca;
	else
		p[d][b] = a, cand[d][a] = ca, r[d][a]++;
}

int dr[] = { 0,1,1, 1, 0,-1,-1,-1 };
int dc[] = { 1,1,0,-1,-1,-1, 0, 1 };
void unionSeatAllDir(int mSeatNum){
	int r = (mSeatNum - 1) / W;
	int c = (mSeatNum - 1) % W;

	for (int d = 0; d < 8; d++)     // 모든 방향별로 다음그룹과 합치는 과정
	{
		int nr = r + dr[d];
		int nc = c + dc[d];

		if (nr < 0 || nr >= H || nc < 0 || nc >= W)
			continue;

		int nextSeatNum = nc + nr * W + 1;
		Union(d, mSeatNum, nextSeatNum);
	}
}

void init(int W, int H){
	::W = W, ::H = H;
	initial(W * H);
}

int selectSeat(int mSeatNum){
	int ans = 0, dist = 100000;
	for (int d = 0; d < 8; d++) {
		int p = find(d, mSeatNum);
		int ca = cand[d][p];
		if (seat[ca] != 0) continue;
		int ndist = calcDist(mSeatNum, ca);
		if (dist > ndist)
			ans = ca, dist = ndist;
	}
	if (ans == 0) return 0;
	unionSeatAllDir(ans);
	seat[ans] = mSeatNum;
	return ans;
}

```
