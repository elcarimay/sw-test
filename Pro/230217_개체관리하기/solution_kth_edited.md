```cpp
#if 1
#include <vector>
#include <algorithm>
#include <queue>
using namespace std;

#define MAXN 10000
#define ACTIVE 1
#define UNACTIVE 0

struct Obj {
	int sr, sc, er, ec, state;
}o[11003];

vector<int> v[11003];
int N, cnt;
void init(int N) {
	::N = N, cnt = 0;
	for (int i = 1; i <= 11000; i++) {
		o[i] = {}; v[i].clear();
	}
}

void addObject(int mID, int y1, int x1, int y2, int x2) {
	cnt++;
	o[mID] = { y1, x1, y2, x2, ACTIVE };
	v[mID].push_back(mID);
}

int group(int mID, int y1, int x1, int y2, int x2) {
	cnt++;
	vector<int> tmp;
	for (int i = 1; i < cnt; i++) {
		if (o[i].state == UNACTIVE) continue;
		if (y1 <= o[i].sr && x1 <= o[i].sc && o[i].er <= y2 && o[i].ec <= x2)
			tmp.push_back(i);
	}
	if (tmp.size() < 2) return 0;
	o[mID] = { N, N, 0, 0 };
	int sum = 0;
	for (int id : tmp) {
		o[mID] = { min(o[mID].sr, o[id].sr), min(o[mID].sc, o[id].sc), max(o[mID].er, o[id].er), max(o[mID].ec, o[id].ec), ACTIVE };
		for (int nx : v[id]) v[mID].push_back(nx);
		o[id].state = UNACTIVE;
		sum += id;
	}
	return sum;
}

int getID(int r, int c) {
	priority_queue<int, vector<int>, less<>> pq;
	for (int i = 1; i <= cnt; i++) {
		if (o[i].state == UNACTIVE) continue;
		if (o[i].sr <= r && r <= o[i].er && o[i].sc <= c && c <= o[i].ec) pq.push(i);
	}
	if (pq.empty()) return 0;
	else return pq.top();
}

int ungroup(int y1, int x1) {
	int id = getID(y1, x1);
	if (id == 0) return 0;
	o[id].state = UNACTIVE;
	for (int nx : v[id]) o[nx].state = ACTIVE;
	return v[id].size();
}

void move(int id, int dr, int dc) {
	o[id].sr += dr, o[id].er += dr, o[id].sc += dc, o[id].ec += dc;
}

int moveObject(int y1, int x1, int y2, int x2) {
	int id = getID(y1, x1);
	if (id == 0) return -1;
	int dr = min(y2 - o[id].sr, N - 1 - o[id].er);
	int dc = min(x2 - o[id].sc, N - 1 - o[id].ec);

	move(id, dr, dc);
	if (v[id].size() > 1)
		for (int nx : v[id]) move(nx, dr, dc);
	return o[id].sr * 10000 + o[id].sc;
}
#endif // 1

```
