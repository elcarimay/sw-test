```cpp
#if 1
#include <queue>
#include <algorithm>
using namespace std;

struct Data {
	int mTime, mId;
	bool operator<(const Data& r) const {
		return (mTime < r.mTime) ||
			(mTime == r.mTime && mId > r.mId);
	}
};

//struct cmp {
//	bool operator()(Data& a, Data& b) {
//		return (a.mTime < b.mTime) ||
//			(a.mTime == b.mTime && a.mId > b.mId);
//	}
//};

int* population; // 인구수
int roadCnt[10005], time[10005]; // roadCnt[id] = id~id+1 사이 도로수 // time[id] = id~id+1 사이 이동시간
//priority_queue<Data, vector<Data>,cmp> pq;
priority_queue<Data> pq;

void init(int N, int mP[]) {
	pq = {};
	population = mP;
	for (int i = 0; i < N - 1; i++) {
		roadCnt[i] = 1;
		time[i] = population[i] + population[i + 1];
		pq.push({ time[i],i });
	}
}

int expand(int M) {
	int id;
	for (int i = 0; i < M; i++) {
		id = pq.top().mId; pq.pop();
		roadCnt[id]++;
		time[id] = (population[id] + population[id + 1]) / roadCnt[id];
		pq.push({ time[id],id });
	}
	return time[id];
}

int calculate(int mFrom, int mTo) {
	int res = 0;
	if (mFrom > mTo) swap(mFrom, mTo);
	for (int i = mFrom; i < mTo; i++) res += time[i];
	return res;
}

bool isPossible(int from, int to, int K, int limit) {
	int gcnt = 1, gsum = 0;
	for (int i = from; i <= to; i++) {
		// 구간내 특정 도시 인구가 limit보다 큰 경우 예외처리
		if (limit < population[i]) return 0;
		gsum += population[i];
		if (gsum > limit) {
			gsum = population[i];
			gcnt++;
			if (gcnt > K) return 0;
		}
	}
	return 1;
}

int divide(int mFrom, int mTo, int K) {
	int s = 1, e = 1e7;
	while (s <= e) {
		int mid = (s + e) / 2;
		if (isPossible(mFrom, mTo, K, mid)) e = mid - 1;
		else s = mid + 1;
	}
	return s;
}
#endif
```
