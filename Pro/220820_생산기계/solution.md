```cpp
/*
	Parametric Search, 지울때 pq 및 unordered_map에 있는것도 같이 지워야 함
*/
#include <vector>
#include <iostream>
#include <string.h>
#include <unordered_map>
#include <algorithm>
#include <queue>
using namespace std;

struct Data {
	int id, time;
	bool operator<(const Data& r) const {
		if (time != r.time) return time < r.time;
		return id < r.id;
	}
};

unordered_map<int, int> mMap;
priority_queue<Data> pq;

void init(int N, int mId[], int mTime[]) {
	mMap.clear(); pq = {};
	for (int i = 0; i < N; i++) {
		mMap[mId[i]] = mTime[i];
		pq.push({ mId[i],mTime[i] });
	}
}

int add(int mId, int mTime) {
	mMap[mId] = mTime;
	pq.push({ mId,mTime });
	return mMap.size();
}

int remove(int K) {
	while (K--) {
		while (true) {
			Data t = pq.top();
			if (mMap.count(t.id) && t.time == mMap[t.id]) break;
			pq.pop();
		}
		Data t = pq.top(); pq.pop();
		mMap.erase(t.id);
	}
	while (true) {
		Data t = pq.top();
		if (mMap.count(t.id) && t.time == mMap[t.id]) break;
		pq.pop();
	}
	return pq.top().id;
}

bool check(int m, int M) {
	for (auto nx : mMap) {
		int cnt = m / nx.second;
		M -= cnt;
		if (M <= 0) return true;
	}
	return false;
}

int produce(int M) {
	int s = 3, e = 1200 * 3000;
	int m = 0;
	while (s <= e) {
		m = (s + e) / 2;
		if (check(m, M)) e = m - 1;
		else s = m + 1;
	}
	return s;
}
```
