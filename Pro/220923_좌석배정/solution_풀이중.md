```cpp
#include <set>
#include <vector>
#include <queue>
#include <unordered_map>
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

vector<Pos> hor[MAXN], ver[MAXN], ld[MAXN * 2], rd[MAXN * 2];
vector<Pos>::iterator it;
int id, idCnt;
priority_queue<Pos> pq;
int r, c;
bool front, back;
void init(int W, int H){
	::W = W, ::H = H, idCnt = 1;
	for (int i = 0; i < MAXN; i++) ver[i].clear(), hor[i].clear();
	for (int i = 0; i < MAXN * 2; i++) ld[i].clear(), rd[i].clear();
	for (int r = 0; r < H; r++) for (int c = 0; c < W; c++) {
		ver[c].push_back({ idCnt }), hor[r].push_back({ idCnt });
		ld[r + c].push_back({ idCnt}), rd[W*H + r-c].push_back({ idCnt});
		db[idCnt++] = { r, c, false };
	}
}

void dir(vector<Pos>& v, int id) {
	front = back = false;
	it = lower_bound(v.begin(), v.end(), Pos{ id });
	if (it == v.end()) front = true; // 앞으로 전진
	if (it == v.begin()) back = true; // 뒤로 전진
	if (it != v.end() && it != v.begin()) front = back = true;
}

int dist(int id, bool opt) { // opt: true면 normal, false면 diagonal
	if (opt) return abs(db[id].r - r) + abs(db[id].c - c);
	else return abs(db[id].r - r);
}

void frontGo(vector<Pos>& v, bool opt, int id, int order) {
	while (front && it != v.begin()) {
		it--;
		if (db[it->id].booked) continue;
		pq.push({ it->id , dist(it->id, opt) , order}); return;
	}
}

void backGo(vector<Pos>& v, bool opt, int id, int order) {
	it = lower_bound(v.begin(), v.end(), Pos{ id });
	while (back) {
		if (!db[it->id].booked) {
			pq.push({ it->id , dist(it->id, opt) , order}); return;
		}
		if (it == --v.end()) return;
		it++;
	}
}

int selectSeat(int mSeatNum){
	r = (mSeatNum - 1) / W, c = (mSeatNum - 1) % W;
	if (!db[mSeatNum].booked) {
		db[mSeatNum].booked = true; return mSeatNum;
	}
	while (!pq.empty()) pq.pop();
	if (hor[r].size()) dir(hor[r], mSeatNum), backGo(hor[r], NORMAL, mSeatNum, 0), frontGo(hor[r], NORMAL, mSeatNum, 4);
	if (ver[c].size()) dir(ver[c], mSeatNum), backGo(ver[c], NORMAL, mSeatNum, 2), frontGo(ver[c], NORMAL, mSeatNum, 6);
	if (ld[r + c].size()) dir(ld[r + c], mSeatNum), backGo(ld[r + c], DIAGONAL, mSeatNum, 3), frontGo(ld[r + c], DIAGONAL, mSeatNum, 7);
	if (rd[W * H + r - c].size()) dir(rd[W * H + r - c], mSeatNum), backGo(rd[W * H + r - c], DIAGONAL, mSeatNum, 1), frontGo(rd[W * H + r - c], DIAGONAL, mSeatNum, 5);
	if (pq.empty()) return 0;
	db[pq.top().id].booked = true;
	return pq.top().id;
}
```
