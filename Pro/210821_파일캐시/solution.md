```cpp
#if 1
#include <vector>
#include <set>
#include <queue>
#include <unordered_map>
#include <algorithm>
using namespace std;

#define LM 20003

struct DB {
	int sIdx, len, tick;
}db[LM];

struct Data {
	int sIdx, len;
	bool operator<(const Data& r)const {
		return sIdx < r.sIdx;
	}
};

unordered_map<int, int> m;
int mCnt;
set<Data> s;
set<Data>::iterator it, fit, bit;
struct Access {
	int id, tick;
	bool operator<(const Access& r)const {
		return tick < r.tick;
	}
};
set<Access> que;
set<Access>::iterator qit[LM];
int totalSize, tick;
void init(int N) {
	totalSize = tick = mCnt = 0, m.clear();
	s.clear(), s.insert({ 0, N }), que.clear();
	for (int i = 0; i < LM; i++) db[i] = { -1,-1 };
}

int access(int fileId, int fileSize) {
	int id, sIdx, len;
	if (m.count(fileId)) id = m[fileId];
	else id = m[fileId] = mCnt++;
	if (db[id].tick != 0) {
		db[id].tick = ++tick;
		que.erase(qit[id]), qit[id] = que.insert({ id, tick }).first;
		return db[id].sIdx;
	}

	it = s.begin();
	while (it != s.end()) {
		if (it->len >= fileSize) break;
		it++;
	}
	if (it == s.end()) {
		while (true) {
			// 큐에서 하나빼서 공간확보
			int cid = que.begin()->id, ctick = que.begin()->tick;
			que.erase(que.begin());
			db[cid].tick = 0, totalSize -= db[cid].len;
			sIdx = db[cid].sIdx, len = db[cid].len;
			it = s.insert({ sIdx, len }).first;
			bool left = false, right = false; // 확보된 공간 좌우비교
			if (it != --s.end()) {
				bit = ++it; it--;
				if (sIdx + len == bit->sIdx) right = true;
			}
			if (it != s.begin()) {
				fit = --it; it++;
				if (fit->sIdx + fit->len == sIdx) left = true;
			}

			if (left == true && right == false) { // 확보된 공간 좌우 연결
				fit = --it; it++;
				sIdx = fit->sIdx, len = fit->len + db[cid].len;
				s.erase(fit);
			}
			else if (left == false && right == true) {
				bit = ++it; it--;
				sIdx = db[cid].sIdx, len = db[cid].len + (bit)->len;
				s.erase(bit);
			}
			else if (left == true && right == true) {
				fit = --it;	bit = ++(++it); it--;
				sIdx = fit->sIdx, len = fit->len + db[cid].len + bit->len;
				s.erase(fit), s.erase(bit);
			}
			if (left == true || right == true)
				s.erase(it), it = s.insert({ sIdx, len }).first;
			if (len >= fileSize) break; // 확보된 공간이 filesize보다 같거나 클경우 탈출
		}
	}
	db[id] = { it->sIdx, fileSize, ++tick }; // it->len >= fileSize
	qit[id] = que.insert({ id, tick }).first, totalSize += fileSize;
	if (it->len > fileSize) s.insert({ it->sIdx + fileSize, it->len - fileSize }); // it->len > fileSize
	s.erase(it);
	return db[id].sIdx;
}

int usage() {
	return totalSize;
}
#endif // 1

```
