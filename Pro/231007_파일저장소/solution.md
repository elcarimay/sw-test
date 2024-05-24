```cpp
#if 1
#include <unordered_map>
#include <vector>
#include <queue>
using namespace std;

struct Data
{
	int sIdx, len;
	bool operator<(const Data& r)const {
		return sIdx > r.sIdx;
	}
};

unordered_map<int, int> idmap; // mId, fid
int idCnt, freeSize; // fid, free space
vector<Data> flist[12005]; // vector<{start, size}> flist[fid]
priority_queue<Data> pq; // 빈조각들 priority_queue<{시작지점, 남은공간}> pq, top => start 작은값

void init(int N) {
	idCnt = 0, pq = {}, pq.push({ 1,freeSize = N });
	idmap.clear();
	for (int i = 0; i < 12005; i++) flist[i].clear();
}

int add(int mId, int mSize) {
	if (freeSize < mSize) return -1;
	freeSize -= mSize;
	idmap[mId] = idCnt;
	flist[idCnt].clear();

	while (mSize) {
		auto cur = pq.top(); pq.pop(); // 남아있는 공간에 대한 정보
		int len = cur.len;
		if (cur.len > mSize) {
			len = mSize;
			pq.push({ cur.sIdx + len, cur.len - len });
		}
		if (flist[idCnt].size() != 0) {
			// 채워져있는 공간바로뒤 idx가 cur.sIdx와 같으면 cur.sIdx, mSize를 더해서 하나로 합침
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
	for (int i = 0; i < idCnt; i++) {
		if (flist[i].size() == 0) continue;
		for (auto next : flist[i]) {
			int s1 = next.sIdx, e1 = next.sIdx + next.len - 1;
			if (s1 <= mEnd && mStart <= e1) { // 구간이 겹치는 조건
				cnt++; break;
			}
		}
	}
	return cnt;
}
#endif
```
