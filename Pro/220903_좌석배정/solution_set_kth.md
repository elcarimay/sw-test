```cpp
#if 1 // 186 ms
#include<set>
using namespace std;

int W, H;
set<int> row[30003], col[30003], ltop[60003], rtop[60003];

void init(int W, int H) {
	::W = W, ::H = H;
	for (int i = 0; i < H; i++) row[i].clear();
	for (int i = 0; i < W; i++) col[i].clear();
	for (int i = 0; i < H + W; i++) ltop[i].clear(), rtop[i].clear();

	int x = 1;
	for (int i = 0; i < H; i++) for (int j = 0; j < W; j++) {
		row[i].insert(x);
		col[j].insert(x);
		ltop[i - j + W].insert(x);
		rtop[i + j].insert(x++);
	}
}

struct Result
{
	int seat, dist, dir;
	bool operator<(const Result& r)const {
		if (dist != r.dist) return dist < r.dist;
		return dir < r.dir;
	}
}ret;

int selectSeat(int seat) {
	int r = (seat - 1) / W, c = (seat - 1) % W;
	ret = { 0, W + H };

	auto it = row[r].lower_bound(seat);
	if (it != row[r].end()) ret = min(ret, {*it, *it - seat, 1});
	if (it != row[r].begin()) ret = min(ret, {*--it, seat - *it, 5});

	it = ltop[r - c + W].lower_bound(seat);
	if (it != ltop[r - c + W].end()) ret = min(ret, { *it, (*it - seat) / (W + 1), 2 });
	if (it != ltop[r - c + W].begin()) ret = min(ret, { *--it, (seat - *it) / (W + 1), 6 });

	it = col[c].lower_bound(seat);
	if (it != col[c].end()) ret = min(ret, { *it, (*it - seat) / W, 3 });
	if (it != col[c].begin()) ret = min(ret, { *--it, (seat - *it) / W, 7 });

	if (W > 1) {
		it = rtop[r + c].lower_bound(seat);
		if (it != rtop[r + c].end()) ret = min(ret, { *it, (*it - seat) / (W - 1), 4 });
		if (it != rtop[r + c].begin()) ret = min(ret, { *--it, (seat - *it) / (W - 1), 8 });
	}

	if (ret.seat) {
		r = (ret.seat - 1) / W, c = (ret.seat - 1) % W;
		row[r].erase(ret.seat);
		col[c].erase(ret.seat);
		ltop[r - c + W].erase(ret.seat);
		rtop[r + c].erase(ret.seat);
	}
	return ret.seat;
}
#endif
```
