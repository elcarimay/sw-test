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
vector<Data> flist[12005]; // fid, data(start, size)
priority_queue<Data> pq;

void init(int N) {
	m.clear(); idCnt = 0; pq = {};
	for (int i = 0; i < 12005; i++) flist[i].clear();
	pq.push({ 1, freeSize = N });
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
	if (freeSize < mSize) return -1;
	freeSize -= mSize;
	int id = getID(mId);

	while (mSize) {
		auto cur = pq.top(); pq.pop();
		int len = cur.len;
		if (cur.len > mSize) {
			len = mSize;
			pq.push({ cur.sIdx + len, cur.len - len });
		}
		int size = flist[id].size();
		if (size != 0) {
			int sIdx = flist[id][size - 1].sIdx + flist[id][size - 1].len;
			if (sIdx == cur.sIdx) {
				flist[id][size - 1].len += len;
			}
			else flist[id].push_back({ cur.sIdx, len });
		}
		else flist[id].push_back({ cur.sIdx, len });
		mSize -= len;
	}
	return flist[id][0].sIdx;
}

int remove(int mId) {
	int id = getID(mId);
	for (auto nx : flist[id]) {
		freeSize += nx.len;
		pq.push(nx);
	}
	int ret = flist[id].size();
	flist[id].clear();
	return ret;
}

int count(int mStart, int mEnd) {
	int ret = 0;
	for (int i = 0; i < idCnt; i++) {
		if (flist[i].empty()) continue;
		for (auto nx : flist[i]) {
			int s = nx.sIdx, e = nx.sIdx + nx.len - 1;
			if (!(e < mStart || mEnd < s)) {
				ret++; break;
			}
		}
	}
	return ret;
}
#endif
```
