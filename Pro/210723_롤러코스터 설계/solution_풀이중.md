```cpp
#include <unordered_map>
#include <string.h>
using namespace std;

int n, r, c, d, order;
struct Pos {
	int order, d;
};

unordered_map<int, Pos> f, b; // r/c hash value, direction
int tf, tb; // top of front, top of back
void init(int n){
	::n = n, f.clear(), b.clear();
	f[n * 2 * n + n] = { 0, 0 }, b[n * 2 * n + n] = { 2,2 };
	tf = tb = n * 2 * n + n;
}

int dr[] = { 0,1,0,-1 }, dc[] = { 1,0,-1,0 };
void addRail(int mFront, int mDirection){ // 0: 후방(front), 1: 전방(back)
	unordered_map<int, Pos>& m = f;
	if (!mFront) r = tf / n, c = tf % n, d = m[tf].d, order = m[tf].d;
	else {
		m = b;
		r = tb / n, c = tb % n, d = m[tb].d, order = m[tb].d;
	}
	if (mDirection == 0) d = (d + 3) % 4;
	else if (mDirection == 2) d = (d + 4) % 3;
	r += dr[d], c += dc[d];
	m[r * 2 * n + c] = { mDirection, d };
	if (mFront) tf = r * 2 * n + c;
	else tb = r * 2 * n + c;
}

int delRail(int mRow, int mCol) {
	int cnt = 0, top;
	bool front = false, back = false;
	r = mRow, c = mCol;
	unordered_map<int, Pos>& m = f;
	int hash = r * 2 * n + c;
	if (b.count(hash)) m = b, top = tb, back = true;
	else if (!f.count(hash)) return 0;
	else top = tf, front = true;
	d = m[hash].d, order = m[hash].order;
	if (order == 0) d++;
	else if (order == 2) d--;
	r -= dr[d], c -= dc[d];
	hash = r * 2 * n + c;
	int start = tf;
	while (true) {
		d = m[start].d, order = m[start].order;
		m.erase(r * 2 * n + c), cnt++;
		if (r * 2 * n + c == mRow * 2 * n + mCol) break;
		if (order == 0) d++;
		else if (order == 2) d--;
		r -= dr[d], c -= dc[d];
	}
	if (front) tf = hash;
	else tb = hash;
	return cnt;
}
```
