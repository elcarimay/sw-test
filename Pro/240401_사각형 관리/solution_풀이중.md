```cpp
#if 1
#include <unordered_map>
#include <set>
#include <vector>
using namespace std;

#define MAXN 10000
#define SIZE 50

struct Rect {
	int mid, r, c, h, w, order;
	bool operator()(const Rect& r)const {
		return order > r.order;
	}
}r[10003]; // id

unordered_map<int, int> m; // mid, id
int mCnt;

struct Cmp {
	bool operator()(const int a, const int b) const {
		return r[a].order > r[b].order;
	}
};
set<int, Cmp> S[MAXN / SIZE][MAXN / SIZE];

void init(int N) {
	for (int i = 0; i < MAXN; i++) r[i] = {};
	m.clear(); mCnt = 0;
	for (int i = 0; i < MAXN / SIZE; i++) for (int j = 0; j < MAXN / SIZE; j++)
		S[i][j].clear();
}

int getID(int mid) {
	unordered_map<int, int>::iterator it = m.find(mid);
	if (it == m.end()) return m[mid] = mCnt++;
	else return m[mid];
}

void addRect(int mID, int mY, int mX, int mHeight, int mWidth) {
	int id = getID(mID);
	int r1 = mY / SIZE, r2 = (mY + mHeight) / SIZE, c1 = mX / SIZE, c2 = (mX + mWidth) / SIZE;
	int gr = mY / SIZE, gc = mX / SIZE;
	r[id] = { mID, mY, mX, mHeight, mWidth, id };
	S[gr][gc].insert(id);
	if (gc + 1 == c2) S[gr][gc + 1].insert(id);// 우측
	if (gr + 1 == r2) S[gr + 1][gc].insert(id);// 아래
	if (gc + 1 == c2 && gr + 1 == r2) S[gr + 1][gc + 1].insert(id);// 우측 아래
}

void selectAndMove(int y1, int x1, int y2, int x2) {
	int gr = y1 / SIZE, gc = x1 / SIZE;
	int gr2 = y2 / SIZE, gc2 = x2 / SIZE;
	int id;
	if (gr == gr2 && gc == gc2) {
		for (set<int, Cmp>::iterator it = S[gr][gc].begin(); it != S[gr][gc].end(); it++) {
			id = *it; S[gr][gc].erase(it); break;
		}
		addRect(r[id].mid, y2, x2, r[id].h, r[id].w); return;
	}
	
	for (set<int, Cmp>::iterator it = S[gr][gc].begin(); it != S[gr][gc].end(); it++) {
		int r1 = r[*it].r, r2 = r[*it].r + r[*it].h;
		int c1 = r[*it].c, c2 = r[*it].c + r[*it].c;
		if (r1 <= y1 && y1 <= r2 && c1 <= x1 && x1 <= c2) {
			id = *it; S[gr][gc].erase(it); break;
		}
	}
	int r1 = y1 / SIZE, r2 = (y1 + r[id].h) / SIZE, c1 = x1 / SIZE, c2 = (x1 + r[id].w) / SIZE;
	if (gc + 1 == c2) {
		for (set<int, Cmp>::iterator it = S[gr][gc + 1].begin(); it != S[gr][gc + 1].end(); it++) {
			int r1 = r[*it].r, r2 = r[*it].r + r[*it].h;
			int c1 = r[*it].c, c2 = r[*it].c + r[*it].c;
			if (r1 <= y1 && y1 <= r2 && c1 <= x1 && x1 <= c2) {
				id = *it; S[gr][gc + 1].erase(it); break;
			}
		}
	}
	if (gr + 1 == r2) {
		for (set<int, Cmp>::iterator it = S[gr + 1][gc].begin(); it != S[gr + 1][gc].end(); it++) {
			int r1 = r[*it].r, r2 = r[*it].r + r[*it].h;
			int c1 = r[*it].c, c2 = r[*it].c + r[*it].c;
			if (r1 <= y1 && y1 <= r2 && c1 <= x1 && x1 <= c2) {
				id = *it; S[gr + 1][gc].erase(it); break;
			}
		}
	}
	if (gc + 1 == c2 && gr + 1 == r2) {
		for (set<int, Cmp>::iterator it = S[gr + 1][gc + 1].begin(); it != S[gr + 1][gc + 1].end(); it++) {
			int r1 = r[*it].r, r2 = r[*it].r + r[*it].h;
			int c1 = r[*it].c, c2 = r[*it].c + r[*it].c;
			if (r1 <= y1 && y1 <= r2 && c1 <= x1 && x1 <= c2) {
				id = *it; S[gr + 1][gc + 1].erase(it); break;
			}
		}
	}
	addRect(r[id].mid, y2, x2, r[id].h, r[id].w);
}

int moveFront(int mID) {
	set<int, Cmp> tmp;
	unordered_map<int, int>::iterator it = m.find(mID);
	if (it == m.end()) return 0;
	int id = it->second; r[id].order = mCnt++;
	int r1 = r[id].r, r2 = (r[id].r + r[id].h);
	int c1 = r[id].c, c2 = (r[id].c + r[id].w);
	int gr = r1 / SIZE, gc = c1 / SIZE;
	if (!S[gr][gc].empty()) { // 현재위치
		for (set<int, Cmp>::iterator it = S[gr][gc].begin(); it != S[gr][gc].end(); it++)
			if (*it == id) { S[gr][gc].erase(it); break; }
		for (int nx : S[gr][gc]) {
			int rr1 = r[nx].r, rr2 = (r[nx].r + r[nx].h);
			int cc1 = r[nx].c, cc2 = (r[nx].c + r[nx].w);
			if (r1 <= rr2 && rr1 <= r2 && c1 <= cc2 && cc1 <= c2) {
				tmp.insert(nx); break;
			}
		}
		S[gr][gc].insert(id);
	}
	if (gc + 1 == c2 / SIZE && !S[gr][gc + 1].empty()) { // 우측
		for (set<int, Cmp>::iterator it = S[gr][gc + 1].begin(); it != S[gr][gc + 1].end(); it++)
			if (*it == id) { S[gr][gc + 1].erase(it); break; }
		for (int nx : S[gr][gc + 1]) {
			int rr1 = r[nx].r / SIZE, rr2 = (r[nx].r + r[nx].h) / SIZE;
			int cc1 = r[nx].c / SIZE, cc2 = (r[nx].c + r[nx].w) / SIZE;
			if (r1 <= rr2 && rr1 <= r2 && c1 <= cc2 && cc1 <= c2) {
				tmp.insert(nx); break;
			}
		}
		S[gr][gc + 1].insert(id);
	}
	if (gr + 1 == r2 / SIZE && !S[gr + 1][gc].empty()) { // 아래
		for (set<int, Cmp>::iterator it = S[gr + 1][gc].begin(); it != S[gr + 1][gc].end(); it++)
			if (*it == id) { S[gr + 1][gc].erase(it); break; }
		for (int nx : S[gr + 1][gc]) {
			int rr1 = r[nx].r / SIZE, rr2 = (r[nx].r + r[nx].h) / SIZE;
			int cc1 = r[nx].c / SIZE, cc2 = (r[nx].c + r[nx].w) / SIZE;
			if (r1 <= rr2 && rr1 <= r2 && c1 <= cc2 && cc1 <= c2) {
				tmp.insert(nx); break;
			}
		}
		S[gr + 1][gc].insert(id);
	}
	if (gr + 1 == r2 / SIZE && gc + 1 == c2 / SIZE && !S[gr + 1][gc + 1].empty()) { // 우측아래
		for (set<int, Cmp>::iterator it = S[gr + 1][gc + 1].begin(); it != S[gr + 1][gc + 1].end(); it++)
			if (*it == id) { S[gr + 1][gc + 1].erase(it); break; }
		for (int nx : S[gr + 1][gc + 1]) {
			int rr1 = r[nx].r / SIZE, rr2 = (r[nx].r + r[nx].h) / SIZE;
			int cc1 = r[nx].c / SIZE, cc2 = (r[nx].c + r[nx].w) / SIZE;
			if (r1 <= rr2 && rr1 <= r2 && c1 <= cc2 && cc1 <= c2) {
				tmp.insert(nx); break;
			}
		}
		S[gr + 1][gc + 1].insert(id);
	}
	return tmp.empty() ? 0 : r[*tmp.begin()].mid;
}

int selectAndErase(int mY, int mX) {
	int gr = mY / SIZE, gc = mX / SIZE;
	if (S[gr][gc].empty()) return 0;
	int id = *S[gr][gc].begin();
	for (set<int, Cmp>::iterator it = S[gr][gc].begin(); it != S[gr][gc].end(); it++)
		if (*it == id) { S[gr][gc].erase(it); break; }
	int r1 = r[id].r / SIZE, r2 = (r[id].r + r[id].h) / SIZE;
	int c1 = r[id].c / SIZE, c2 = (r[id].r + r[id].w) / SIZE;
	if (gc + 1 == c2) S[gr][gc + 1].erase(id);// 우측
	if (gr + 1 == r2) S[gr + 1][gc].erase(id);// 아래
	if (gc + 1 == c2 && gr + 1 == r2) S[gr + 1][gc + 1].erase(id);// 오측 아래
	return r[id].mid;
}

int check(int mY, int mX) {
	int gr = mY / SIZE, gc = mX / SIZE;
	if (S[gr][gc].empty()) return 0;
	return r[*S[gr][gc].begin()].mid;
}
#endif // 1

```
