```cpp
#include <set>
#include <unordered_map>
#include <queue>
using namespace std;

struct Data
{
	int id, start, end;
	bool operator<(const Data&data)const {
		return (start < data.start)||
			(start == data.start && id < data.id);
	}
};

set<Data> s;
unordered_map<int, int> hmap; // id, start
priority_queue<int, vector<int>,greater<int>> pq;

void init() {
	s.clear(); hmap.clear();
}

int add(int mId, int mStart, int mEnd) {
	if (hmap.count(mId)) s.erase({ mId,hmap[mId] });
	s.insert({ mId, mStart, mEnd });
	hmap[mId] = mStart;
	return hmap.size();
}

int remove(int mId) {
	if (hmap.count(mId)) {
		s.erase({ mId,hmap[mId] });
		hmap.erase(mId);
	}
	return hmap.size();
}

int announce(int mDuration, int M) {
	pq = {};
	for (const Data& d: s){
		pq.push(d.end);
		int endTime = d.start + mDuration - 1;
		while (!pq.empty() && pq.top() < endTime) pq.pop();
		if (pq.size() >= M) return d.start;
	}
	return -1;
}
```
