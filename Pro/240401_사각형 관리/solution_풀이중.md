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
	int id = *S[gr][gc].begin();
	int r1 = r[id].r, r2 = (r[id].r + r[id].h);
	int c1 = r[id].c, c2 = (r[id].c + r[id].w);
	for (int i = -1; i <= 1; i++) for (int j = -1; j <= 1; j++) {
		if (gr + i < 0 || gr + i >= MAXN / SIZE || gc + j < 0 || gc + j >= MAXN / SIZE) continue;
		for (set<int, Cmp>::iterator it = S[gr + i][gc + j].begin(); it != S[gr + i][gc + j].end(); it++)
			if (*it == id) {
				S[gr + i][gc + j].erase(it); break;
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
	for (int i = -1; i <= 1; i++) for (int j = -1; j <= 1; j++) {
		if (gr + i < 0 || gr + i >= MAXN / SIZE || gc + j < 0 || gc + j >= MAXN / SIZE) continue;
		bool flag = false;
		for (set<int, Cmp>::iterator it = S[gr + i][gc + j].begin(); it != S[gr + i][gc + j].end(); it++)
			if (*it == id) {
				S[gr + i][gc + j].erase(it); flag = true;
			}
			else {
				tmp.insert(*S[gr + i][gc + j].begin()); break;
			}
		if (flag) S[gr + i][gc + j].insert(id);
	}
	return tmp.empty() ? 0 : r[*tmp.begin()].mid;
}

int selectAndErase(int mY, int mX) {
	int gr = mY / SIZE, gc = mX / SIZE;
	int id = *S[gr][gc].begin();
	int r1 = r[id].r, r2 = (r[id].r + r[id].h);
	int c1 = r[id].c, c2 = (r[id].c + r[id].w);
	for (int i = -1; i <= 1; i++) for (int j = -1; j <= 1; j++) {
		if (gr + i < 0 || gr + i >= MAXN / SIZE || gc + j < 0 || gc + j >= MAXN / SIZE) continue;
		for (set<int, Cmp>::iterator it = S[gr + i][gc + j].begin(); it != S[gr + i][gc + j].end(); it++)
			if (*it == id) {
				S[gr + i][gc + j].erase(it); break;
			}
	}
	return r[id].mid;
}

int check(int mY, int mX) {
	int gr = mY / SIZE, gc = mX / SIZE;
	if (S[gr][gc].empty()) return 0;
	return r[*S[gr][gc].begin()].mid;
}
#endif // 1

#endif // 1

```
