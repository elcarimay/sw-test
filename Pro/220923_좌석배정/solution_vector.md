```cpp
#if 1
#include <vector>
using namespace std;

#define MAXN 30000
#define NORMAL true
#define DIAGONAL false

struct DB {
	int r, c;
	bool booked;
}db[MAXN + 3];

int W, H;

struct Pos {
	int id, d, order;
	bool operator==(const Pos& re)const {
		return id == re.id && d == re.d;
	}
	bool operator<(const Pos& re)const {
		if (d != re.d) return d > re.d;
		if (order != re.order) return order > re.order;
		if (db[id].r != db[re.id].r) return db[id].r < db[re.id].r;
		return db[id].c < db[re.id].c;
	}
};

vector<int> hor[MAXN], ver[MAXN], ld[MAXN * 2], rd[MAXN * 2];
vector<int>::iterator it, fit;
int id, idCnt, d, order, nd;
int r, c;
bool front, back;
void init(int W, int H) {
	::W = W, ::H = H, idCnt = 1;
	for (int i = 0; i < MAXN; i++) ver[i].clear(), hor[i].clear();
	for (int i = 0; i < MAXN * 2; i++) ld[i].clear(), rd[i].clear();
	for (int r = 0; r < H; r++) for (int c = 0; c < W; c++) {
		ver[c].push_back(idCnt), hor[r].push_back(idCnt);
		ld[r + c].push_back(idCnt), rd[W * H + r - c].push_back(idCnt);
		db[idCnt++] = { r, c, false };
	}
}

void dir(vector<int>& v, int id) {
	front = back = false;
	it = lower_bound(v.begin(), v.end(), id );
	fit = it;
	if (it == v.end()) front = true; // 앞으로 전진
	if (it == v.begin()) back = true; // 뒤로 전진
	if (it != v.end() && it != v.begin()) front = back = true;
}

int dist(int id, bool opt) { // opt: true면 normal, false면 diagonal
	if (opt) return abs(db[id].r - r) + abs(db[id].c - c);
	else return abs(db[id].r - r);
}

void frontGo(vector<int>& v, bool opt, int new_order) {
	while (front && fit != v.begin()) {
		fit--;
		if (db[*fit].booked) continue;
		nd = dist(*fit, opt);
		if (d > nd || (d == nd && order > new_order)) {
			d = nd, id = *fit, order = new_order; return;
		}
	}
}

void backGo(vector<int>& v, bool opt, int new_order) {
	while (back) {
		nd = dist(*it, opt);
		if (db[*it].booked == false && (d > nd || (d == nd && order > new_order))) {
			d = nd, id = *it, order = new_order; return;
		}
		if (it == --v.end()) return;
		it++;
	}
}

int selectSeat(int mSeatNum) {
	r = (mSeatNum - 1) / W, c = (mSeatNum - 1) % W;
	if (!db[mSeatNum].booked) {
		db[mSeatNum].booked = true;
		return mSeatNum;
	}
	id = -1, d = INT_MAX, order = 9;
	dir(hor[r], mSeatNum), backGo(hor[r], NORMAL, 0), frontGo(hor[r], NORMAL, 4);
	dir(ver[c], mSeatNum), backGo(ver[c], NORMAL, 2), frontGo(ver[c], NORMAL, 6);
	dir(ld[r + c], mSeatNum), backGo(ld[r + c], DIAGONAL, 3), frontGo(ld[r + c], DIAGONAL, 7);
	dir(rd[W * H + r - c], mSeatNum), backGo(rd[W * H + r - c], DIAGONAL, 1), frontGo(rd[W * H + r - c], DIAGONAL, 5);
	if (id == -1) return 0;
	db[id].booked = true;
	return id;
}
#endif // 1

```
