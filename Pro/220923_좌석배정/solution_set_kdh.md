```cpp
#include <algorithm>
#include <set>
using namespace std;

#define MAXN 30000
#define NORMAL true
#define DIAGONAL false

set<int> hor[MAXN], ver[MAXN], ld[MAXN * 2], rd[MAXN * 2];
set<int>::iterator it;
int W, H, r, c, id, idCnt, d, order, nd;
bool front, back;
void init(int W, int H) {
	::W = W, ::H = H, idCnt = 1;
	for (int i = 0; i < W; i++) ver[i].clear();
	for (int i = 0; i < H; i++) hor[i].clear();
	for (int i = 0; i < W + H; i++) ld[i].clear(), rd[i].clear();
	for (int r = 0; r < H; r++) for (int c = 0; c < W; c++) {
		ver[c].insert(idCnt), hor[r].insert(idCnt);
		ld[r + c].insert(idCnt), rd[W + r - c].insert(idCnt++);
	}
}

int dist(int id, bool opt) { // opt: true면 normal, false면 diagonal
	int nr = (id - 1) / W, nc = (id - 1) % W; // new r, c
	if (opt) return abs(nr - r) + abs(nc - c);
	else return abs(nr - r);
}

void dir(set<int>& v, int id) {
	front = back = false;
	it = v.lower_bound(id); //it = lower_bound(v.begin(), v.end(), id); 이렇게 쓰면 느림
	if (!v.size()) return;
	if (v.size() == 1) {
		(it == v.end()) ? front = true : back = true; return;
	}
	if (it == v.end()) front = true; // 앞으로 전진
	if (it == v.begin()) back = true; // 뒤로 전진
	if (it != v.end() && it != v.begin()) front = back = true;
}

void compare(bool opt, int new_order) {
	if (d > nd || (d == nd && order > new_order))
		d = nd, id = *it, order = new_order;
}

void backGo(set<int>& v, bool opt, int new_order) {
	if (!back) return;
	if (v.size() == 1) nd = dist(*it, opt), compare(opt, new_order);
	else nd = dist(*it, opt), compare(opt, new_order);
}

void frontGo(set<int>& v, bool opt, int new_order) {
	if (!front) return;
	if (v.size() == 1) nd = dist(*--it, opt), compare(opt, new_order);
	else nd = dist(*--it, opt), compare(opt, new_order);
}

int selectSeat(int mSeatNum) {
 	r = (mSeatNum - 1) / W, c = (mSeatNum - 1) % W;
	id = 0, d = INT_MAX, order = 9;
	dir(hor[r], mSeatNum), backGo(hor[r], NORMAL, 0), frontGo(hor[r], NORMAL, 4);
	dir(ver[c], mSeatNum), backGo(ver[c], NORMAL, 2), frontGo(ver[c], NORMAL, 6);
	dir(ld[r + c], mSeatNum), backGo(ld[r + c], DIAGONAL, 3), frontGo(ld[r + c], DIAGONAL, 7);
	dir(rd[W + r - c], mSeatNum), backGo(rd[W + r - c], DIAGONAL, 1), frontGo(rd[W + r - c], DIAGONAL, 5);
	if (id != 0) {
		r = (id - 1) / W, c = (id - 1) % W;
		hor[r].erase(id), ver[c].erase(id), ld[r + c].erase(id), rd[W + r - c].erase(id);
	}
	return id;
}
```
