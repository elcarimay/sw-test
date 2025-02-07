```cpp
#include <vector>
#include <set>
#include <queue>
#include <unordered_map>
#include <algorithm>
using namespace std;

struct Data {
	int len, start;
	bool operator==(const Data& r)const {
		return start == r.start && len == r.len;
	}
	bool operator<(const Data& r)const {
		return start < r.start;
	}
};

unordered_map<int, int> m;
int mCnt;

Data db[20003];

vector<Data> blank;
int N, max_value;
queue<int> que;
void init(int N) {
	::N = N, mCnt = 0, m.clear();
	blank.push_back({ 0, N });
	max_value = N;
}

int getID(int f) {
	if (m.count(f)) return m[f];
	else return m[f] = mCnt++;
}

int access(int fileId, int fileSize) {
	if (m.count(fileId)) {
		int tmp = que.front(); que.pop(); que.push(tmp); return 0;
	}
	int id = m[fileId] = mCnt++;
	que.push(fileId);
	int size = blank.size();
	if (max_value >= fileSize) { // 넣을때가 있는경우
		for (int i = 0; i < size; i++) {
			db[id] = { blank[i].start, fileSize };
			blank[i].start += fileSize;
			blank[i].len -= fileSize;
		}
		max_value = -1;
		for (int i = 0; i < blank.size(); i++) max_value = max(max_value, blank[i].len);
		return db[id].start;
	}
	else {// 넣을때가 없는경우
		auto it = lower_bound(blank.begin(), blank.end(), Data{ fileSize });
		it = lower_bound(blank.begin(), blank.end(), Data{ fileSize });
		int idx = it - blank.begin();
		bool left = false, right = false;
		if (it == blank.begin() && it != blank.end()) right = true;
		if (it != blank.begin() && it == blank.end()) left = true;
		if (it != blank.begin() && it != blank.end()) left = right = true;
		if (left == true && right == true) {
			blank[idx].len += db[id].len + blank[idx + 1].len;

			blank.erase(++it);
		}
		else if (left) {
			blank[idx].len += db[id].len;
			blank.erase(it);
		}
		else if (right) {

		}
	}
	return 0;
}

int usage() {
	int cnt = 0;
	for (auto nx : blank) {
		cnt += nx.len;
	}
	return N - cnt;
}
```
