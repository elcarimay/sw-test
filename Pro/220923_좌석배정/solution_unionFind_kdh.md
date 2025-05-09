```cpp
#if 1
// row = (seatNum - 1) / W, col = (seatNum - 1) % W
// 방향별로 부모가 존재하며 각 부모별 그러니까 그룹별로 추천번호가 존재함
// 그룹을 합칠 때 가장 오른쪽 번호가 그 다음 추천 번호가 됨
// dr, dc벡터를 정할때 1~8번까지 방향을 고려해서 정해야 함
// 1~4번(이때 d는 0~3) 방향까지는 그 다음 번호를 추천번호로 정해야 하기때문에 가장 높은 숫자로 선택해야 함
// 그러나 5~8번까지는 그 이전 번호를 추천전호로 정해야 하기 때문에 가장 낮은 숫자로 선택해야 함
// 자리가 배정이 되면 상기 추천번호에 해당되는 배열에 배정할 번호를 등록함
// 이후 상기 추천번호를 방향별로 전부 union해줌
#include <iostream>
using namespace std;

#define MAXN 30003
int seat[MAXN], p[8][MAXN], r[8][MAXN], cand[8][MAXN], W, H;
int calcDist(int a, int b) {
	int dr = (a - 1) / W - (b - 1) / W;
	int dc = (a - 1) % W - (b - 1) % W;
	return max(abs(dr), abs(dc));
}
void init(int W, int H) {
	::W = W, ::H = H;
	for (int i = 1; i <= W * H; i++) {
		seat[i] = 0;
		for (int d = 0; d < 8; d++)
			p[d][i] = cand[d][i] = i, r[d][i] = 0;
	}
}

int find(int d, int seatNum) {
	if (p[d][seatNum] == seatNum) return seatNum;
	return p[d][seatNum] = find(d, p[d][seatNum]);
}

void Union(int d, int a, int b) {
	int x = find(d, a), y = find(d, b);
	if (x == y) return;
	int ca = d < 4 ? max(cand[d][x], cand[d][y]) : min(cand[d][x], cand[d][y]);
	if (r[d][x] < r[d][y]) swap(x, y);
	p[d][y] = x, cand[d][x] = ca;
	if (r[d][x] == r[d][y]) r[d][x]++;
}


int dr[] = { 0,1,1, 1, 0,-1,-1,-1 };
int dc[] = { 1,1,0,-1,-1,-1, 0, 1 };
void unionSeatAllDir(int mSeatNum) {
	int r = (mSeatNum - 1) / W, c = (mSeatNum - 1) % W;
	for (int d = 0; d < 8; d++) {
		int nr = r + dr[d], nc = c + dc[d];
		if (nr < 0 || nr >= H || nc < 0 || nc >= W) continue;
		int nextSeatNum = nc + nr * W + 1;
		Union(d, mSeatNum, nextSeatNum);
	}
}

int selectSeat(int mSeatNum) {
	int ans = 0, dist = 100000;
	for (int d = 0; d < 8; d++) {
		int p = find(d, mSeatNum), ca = cand[d][p];
		if (seat[ca] != 0) continue;
		int ndist = calcDist(mSeatNum, ca);
		if (dist > ndist) ans = ca, dist = ndist;
	}
	if (ans == 0) return 0;
	unionSeatAllDir(ans);
	seat[ans] = mSeatNum;
	return ans;
}
#endif // 1
```
