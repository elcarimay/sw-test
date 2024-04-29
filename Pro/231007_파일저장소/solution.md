```cpp
#if 1
#include <unordered_map>
#include <vector>
#include <queue>
using namespace std;

struct Data
{
	int sIdx, len; // start idx, size
	bool operator<(const Data& data)const {
		return sIdx > data.sIdx;
	}
};

unordered_map<int, int> idmap; // mid, fid
int idCnt, freeSize;
vector<Data> flist[12005];
priority_queue<Data> pq;

void init(int N) {
	idCnt = 0, pq = {},	pq.push({ 1,freeSize = N });
	idmap.clear();
	for (int i = 0; i < 12005; i++) flist[i].clear();
}

int add(int mId, int mSize) {
	if (freeSize < mSize) return -1;
	freeSize -= mSize;
	idmap[mId] = idCnt;
	flist[idCnt].clear();

	while (mSize) {
		auto cur = pq.top(); pq.pop();
		int len = cur.len;
		if (cur.len > mSize) {
			len = mSize;
			pq.push({ cur.sIdx + len, cur.len - len});
		}
		if (flist[idCnt].size() != 0) {
			int size = flist[idCnt][flist[idCnt].size() - 1].sIdx + flist[idCnt][flist[idCnt].size() - 1].len;
			if (size == cur.sIdx) {
				flist[idCnt][flist[idCnt].size() - 1].len += len;
			}
			else flist[idCnt].push_back({ cur.sIdx, len });
		}
		else flist[idCnt].push_back({ cur.sIdx, len });
		mSize -= len;
	}
	return flist[idCnt++][0].sIdx;
}

int remove(int mId) {
	int ret = flist[idmap[mId]].size();
	for (auto nx : flist[idmap[mId]]) {
		pq.push(nx); freeSize += nx.len;
	}
	flist[idmap[mId]].clear();
	return ret;
}

int count(int mStart, int mEnd) {
	int cnt = 0;
	for (int i = 0; i < idCnt; i++){
		if (flist[i].size() == 0) continue;
		for (auto next : flist[i]) {
			int s1 = next.sIdx, e1 = next.sIdx + next.len-1;
			if (s1 <= mEnd && mStart <= e1) {
				cnt++; break;
			}
		}
	}
	return cnt;
}
#endif
```
