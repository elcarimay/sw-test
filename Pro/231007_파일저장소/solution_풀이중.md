```cpp
#if 1
#include <unordered_map>
#include <vector>
#include <queue>
using namespace std;

struct Data
{
	int sIdx, len; // start idx, file size
	bool operator<(const Data& r)const {
		return sIdx > r.sIdx;
	}
};

unordered_map<int, int> m; // id, fid
int idCnt, freeSize;
vector<Data> flist[12005];
priority_queue<Data> pq;

void init(int N) {
	m.clear(); idCnt = 0;
	for (int i = 0; i < N; i++) flist[i].clear();
	pq = {1, freeSize = N};
}

int getID(int mid) {
	int id;
	auto it = m.find(mid);
	if (it == m.end()) {
		id = idCnt;
		m[mid] = idCnt++;
	}
	else id = m[mid];
	return id;
}

int add(int mId, int mSize) {
	int id = getID(mId);
	while (!pq.empty()) {
		Data cur = pq.top(); pq.pop();
		if (cur.sIdx >= mSize) {
			pq.push({ cur.sIdx + mSize, cur.len - mSize });
			flist[id].push_back({ mId, mSize });
			freeSize -= mSize;
			return cur.sIdx + mSize;
		}
		pq.push(cur);
	}
	return -1;
}

int remove(int mId) {
	int id = getID(mId);
	auto& f = flist[id];
	if (f.size() == 1) return 1;
	sort(f.begin(), f.end());
	int sidx, len;
	for (int i = 0; i < f.size() - 1; i++) {
		if (f[i].sIdx + f[i].len == f[i + 1].sIdx) {
			sidx = f[i].sIdx;
			len = f[i].len + f[i + 1].len;
		}
		else {

		}
		i;
	}
	int size = flist[id].size(); flist[id].clear();
	return size;
}

int count(int mStart, int mEnd) {
	return 0;
}
#endif
```
