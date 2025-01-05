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
int mCnt, oCnt;

struct Data {
	int id, order;
	bool operator<(const Data& r) const {
		return order > r.order;
	}
};
set<Data> S[MAXN / SIZE][MAXN / SIZE];
set<Data> tmp;
void init(int N) {
	for (int i = 0; i < N; i++) r[i] = {};
	m.clear(); mCnt = 0, oCnt = 0;
	for (int i = 0; i < N / SIZE; i++) for (int j = 0; j < N / SIZE; j++)
		S[i][j].clear();
}

int getID(int mid) {
	unordered_map<int, int>::iterator it = m.find(mid);
	if (it == m.end()) return m[mid] = mCnt++;
	else return m[mid];
}

void addRect(int mID, int mY, int mX, int mHeight, int mWidth) {
	int id = getID(mID);
	int r1 = mY / SIZE, r2 = (mY + mHeight-1) / SIZE, c1 = mX / SIZE, c2 = (mX + mWidth-1) / SIZE;
	int gr = mY / SIZE, gc = mX / SIZE;
	int new_order = oCnt++;
	r[id] = { mID, mY, mX, mHeight, mWidth, new_order };
	S[gr][gc].insert({ id, new_order });
	if (gc + 1 == c2) S[gr][gc + 1].insert({ id, new_order });// 우측
	if (gr + 1 == r2) S[gr + 1][gc].insert({ id, new_order });// 아래
	if (gc + 1 == c2 && gr + 1 == r2) S[gr + 1][gc + 1].insert({ id, new_order });// 우측 아래
}

void selectAndMove(int y1, int x1, int y2, int x2) { // 문제에 표시되어 있지 않지만 없는경우도 고려해야 함
	tmp.clear();
	int gr = y1 / SIZE, gc = x1 / SIZE;
	for (int i = -1; i <= 1; i++) for (int j = -1; j <= 1; j++) {
		if (gr + i < 0 || gr + i >= MAXN / SIZE || gc + j < 0 || gc + j >= MAXN / SIZE) continue;
		for (set<Data>::iterator it = S[gr + i][gc + j].begin(); it != S[gr + i][gc + j].end(); it++) {
			int rr1 = r[it->id].r, rr2 = (r[it->id].r + r[it->id].h - 1);
			int cc1 = r[it->id].c, cc2 = (r[it->id].c + r[it->id].w - 1);
			if (rr1 <= y1 && y1 <= rr2 && cc1 <= x1 && x1 <= cc2) {
				tmp.insert({ it->id,r[it->id].order });
				S[gr + i][gc + j].erase(it);
				break;
			}
		}
	}
	if (!tmp.empty()) {
		int id = tmp.begin()->id;
		addRect(r[id].mid, y2, x2, r[id].h, r[id].w);
	}
}

int moveFront(int mID) {
	tmp.clear();
	unordered_map<int, int>::iterator it = m.find(mID);
	if (it == m.end()) return 0;
	int id = it->second, old_order = r[id].order;
	r[id].order = oCnt++;
	int r1 = r[id].r, r2 = (r[id].r + r[id].h - 1);
	int c1 = r[id].c, c2 = (r[id].c + r[id].w - 1);
	int gr = r1 / SIZE, gc = c1 / SIZE;
	for (int i = 0; i <= 1; i++) for (int j = 0; j <= 1; j++) {
		if (gr + i > r2 / SIZE || gc + j > c2 / SIZE) continue;
		if (gr + i >= MAXN / SIZE || gc + j >= MAXN / SIZE) continue;
		bool flag = false; bool flag2 = false;
		for (set<Data>::iterator it = S[gr + i][gc + j].begin(); it != S[gr + i][gc + j].end(); it++) {
			if (flag2 == false) {
				if (it-> id == id) flag = true;
				else {
					int rr1 = r[it->id].r, rr2 = (r[it->id].r + r[it->id].h - 1);
					int cc1 = r[it->id].c, cc2 = (r[it->id].c + r[it->id].w - 1);
					if (r1 <= rr2 && rr1 <= r2 && c1 <= cc2 && cc1 <= c2) {
						flag2 = true; tmp.insert({ it->id, r[it->id].order });
					}
				}
			}
			if (flag == true && flag2 == true) break;
		}
		if (flag) {
			S[gr + i][gc + j].erase(S[gr + i][gc + j].lower_bound({ id, old_order }));
			S[gr + i][gc + j].insert({ id, r[id].order });
		}
	}
	return tmp.empty() ? 0 : r[tmp.begin()->id].mid;
}

int selectAndErase(int mY, int mX) {
	tmp.clear();
	int gr = mY / SIZE, gc = mX / SIZE;
	for (int i = -1; i <= 1; i++) for (int j = -1; j <= 1; j++) {
		if (gr + i < 0 || gr + i >= MAXN / SIZE || gc + j < 0 || gc + j >= MAXN / SIZE) continue;
		for (set<Data>::iterator it = S[gr + i][gc + j].begin(); it != S[gr + i][gc + j].end(); it++) {
			int r1 = r[it->id].r, r2 = (r[it->id].r + r[it->id].h - 1);
			int c1 = r[it->id].c, c2 = (r[it->id].c + r[it->id].w - 1);
			if (r1 <= mY && mY <= r2 && c1 <= mX && mX <= c2) {
				tmp.insert({ it->id, r[it->id].order });
				S[gr + i][gc + j].erase(it); break;
			}
		}
	}
	return tmp.empty() ? 0 : r[tmp.begin()->id].mid;
}

int check(int mY, int mX) {
	int gr = mY / SIZE, gc = mX / SIZE; int id = -1;
	if (S[gr][gc].empty()) return 0;
	tmp.clear();
	for (set<Data>::iterator it = S[gr][gc].begin(); it != S[gr][gc].end(); it++) {
		int r1 = r[it->id].r, r2 = (r[it->id].r + r[it->id].h - 1);
		int c1 = r[it->id].c, c2 = (r[it->id].c + r[it->id].w - 1);
		if (r1 <= mY && mY <= r2 && c1 <= mX && mX <= c2) tmp.insert({ it->id, r[it->id].order });
	}
	return tmp.empty() ? 0 : r[tmp.begin()->id].mid;
}
#endif // 1

```
