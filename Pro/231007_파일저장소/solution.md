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
int idCnt, N;
vector<Data> flist[36000 + 5];
priority_queue<Data> pq;

void init(int _N) {
	N = _N, idCnt = 0, pq = {}, pq.push({ 1,N });
	idmap.clear();
}

int add(int mId, int mSize) {
	int rest = 0;
	idmap[mId] = idCnt;
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		if (cur.len > mSize) {
			flist[idCnt].push_back({ cur.sIdx, mSize });
			cur.len -= mSize, cur.sIdx += mSize;
			pq.push(cur);
			return flist[idCnt++][0].sIdx;
		}
		else if (cur.len == mSize) {
			for (auto nx : flist[idCnt]) {
				if (nx.sIdx == cur.sIdx + cur.len) {
					cur.len += nx.len;
					flist[idCnt].push_back(cur);
					return flist[idCnt++][0].sIdx;
				}
			}
		}
		else {
			if (flist[idCnt].size() != 0) {
				int size = flist[idCnt][flist[idCnt].size() - 1].sIdx + flist[idCnt][flist[idCnt].size() - 1].len;
				if (size == cur.sIdx) {
					flist[idCnt][flist[idCnt].size() - 1].len + cur.len;
				}
			}
			else flist[idCnt].push_back(cur);
			mSize -= cur.len;
		}
	}
	return -1;
}

int remove(int mId) {
	int ret = flist[idmap[mId]].size();
	for (auto nx : flist[idmap[mId]]) {
		pq.push(nx);
	}
	flist[idmap[mId]].clear();
	return ret;
}

int count(int mStart, int mEnd) {
	int cnt = 0;
	for (int i = 0; i < idCnt; i++){
		if (flist[i].size() == 0) continue;
		for (auto next : flist[i]) {
			int s1 = next.sIdx, e1 = next.sIdx + next.len;
			if (s1 <= mEnd && mStart <= e1) {
				cnt++; break;
			}
		}
	}
	return cnt;
}
#endif
```
