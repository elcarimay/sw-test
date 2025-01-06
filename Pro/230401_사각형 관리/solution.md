```cpp
#if 1
#include <set>
using namespace std;

#define MAXN 10000
#define SIZE 50

struct Rect {
	int r, c, h, w, order;
}r[10003]; // id

int mCnt, oCnt;

struct Data {
	int id, order;
	bool operator<(const Data& r) const {
		return order > r.order;
	}
};

set<Data> S[MAXN / SIZE + 1][MAXN / SIZE + 1];
set<Data> tmp;
void init(int N) {
	mCnt = oCnt = 1;
	for (int i = 0; i <= MAXN / SIZE; i++) for (int j = 0; j <= MAXN / SIZE; j++)
		S[i][j].clear();
}

void addRect(int mID, int mY, int mX, int mHeight, int mWidth) {
	int er = (mY + mHeight - 1) / SIZE, ec = (mX + mWidth - 1) / SIZE;
	int gr = mY / SIZE, gc = mX / SIZE;
	int id, new_order;
	if (mID >= mCnt) id = mCnt++, new_order = oCnt++;
	else id = mID, new_order = r[id].order;
	r[id] = { mY, mX, mHeight, mWidth, new_order };
	S[gr][gc].insert({ id, new_order });
	if (gc + 1 == ec) S[gr][gc + 1].insert({ id, new_order });// 우측
	if (gr + 1 == er) S[gr + 1][gc].insert({ id, new_order });// 아래
	if (gc + 1 == ec && gr + 1 == er) S[gr + 1][gc + 1].insert({ id, new_order });// 우측 아래
}

struct Pos {
	int sr, er, sc, ec;
};

Pos pos(int id) {
	return { r[id].r, r[id].r + r[id].h - 1, r[id].c, r[id].c + r[id].w - 1 };
}
Pos p, p1;
void selectAndMove(int y1, int x1, int y2, int x2) {
	tmp.clear();
	int gr = y1 / SIZE, gc = x1 / SIZE;
	for (int i = -1; i <= 1; i++) for (int j = -1; j <= 1; j++) {
		if (gr + i < 0 || gr + i >= MAXN / SIZE || gc + j < 0 || gc + j >= MAXN / SIZE) continue;
		for (set<Data>::iterator it = S[gr + i][gc + j].begin(); it != S[gr + i][gc + j].end(); it++) {
			p = pos(it->id);
			if (p.sr <= y1 && y1 <= p.er && p.sc <= x1 && x1 <= p.ec) { // sr: start of row, er: end of row
				tmp.insert({ it->id,r[it->id].order });
				S[gr + i][gc + j].erase(it);
				break;
			}
		}
	}
	if (!tmp.empty()) {
		int id = tmp.begin()->id;
		addRect(id, y2, x2, r[id].h, r[id].w);
	}
}

int moveFront(int mID) {
	tmp.clear();
	if (mID >= mCnt) return 0;
	p = pos(mID);
	int gr = p.sr / SIZE, gc = p.sc / SIZE;
	int old_order = r[mID].order; r[mID].order = oCnt++;
	for (int i = 0; i <= 1; i++) for (int j = 0; j <= 1; j++) {
		bool flag = false; bool flag2 = false;
		if (!S[gr + i][gc + j].count({ mID, old_order })) continue;
		for (set<Data>::iterator it = S[gr + i][gc + j].begin(); it != S[gr + i][gc + j].end(); it++) {
			if (it->id == mID) flag = true;
			if (flag2 == false && it->id != mID) {
				p1 = pos(it->id);
				if (p.sr <= p1.er && p1.sr <= p.er && p.sc <= p1.ec && p1.sc <= p.ec) {
					flag2 = true; tmp.insert({ it->id, r[it->id].order });
				}
			}
			if (flag == true && flag2 == true) break;
		}
		if (flag) {
			S[gr + i][gc + j].erase(S[gr + i][gc + j].lower_bound({ mID, old_order }));
			S[gr + i][gc + j].insert({ mID, r[mID].order });
		}
	}
	return tmp.empty() ? 0 : tmp.begin()->id;
}

int check(int mY, int mX) {
	int gr = mY / SIZE, gc = mX / SIZE;
	if (S[gr][gc].empty()) return 0;
	tmp.clear();
	for (set<Data>::iterator it = S[gr][gc].begin(); it != S[gr][gc].end(); it++) {
		p = pos(it->id);
		if (p.sr <= mY && mY <= p.er && p.sc <= mX && mX <= p.ec) tmp.insert({ it->id, r[it->id].order });
	}
	return tmp.empty() ? 0 : tmp.begin()->id;
}

int selectAndErase(int mY, int mX) {
	int id = check(mY, mX);
	if (id == 0) return 0;
	tmp.clear();
	p = pos(id);
	int gr = p.sr / SIZE, gc = p.sc / SIZE;
	for (int i = 0; i <= 1; i++) for (int j = 0; j <= 1; j++) {
		if (!S[gr + i][gc + j].count({ id, r[id].order })) continue;
		S[gr + i][gc + j].erase(S[gr + i][gc + j].lower_bound({ id,r[id].order }));
	}
	return id;
}
#endif // 1
```
