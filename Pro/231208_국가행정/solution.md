```cpp
#include <queue>
using namespace std;

#define MAX_CITIES 10000
#define MAX_BLOCKS 101

inline int max(int a, int b) { return a < b ? b : a; }
inline int min(int a, int b) { return a > b ? b : a; }
inline int ceil(int a, int b) { return (a + b - 1) / b; }

int cities[MAX_CITIES];

struct Road
{
	int rId, time, numRoads;
	void expand() {
		numRoads += 1;
		time = (cities[rId + 1] + cities[rId]) / numRoads;
	}
	bool operator<(const Road& road)const {
		return (time < road.time) ||
			(time == road.time && rId > road.rId);
	}
};

struct Partition
{
	int n, bSize, bCnt, arr[MAX_CITIES], blocks[MAX_BLOCKS];
	void init(int _N) {
		n = _N;	bSize = sqrt(n); bCnt = ceil(n, bSize);
		for (int i = 0; i < n; i++) arr[i] = 0;
		for (int i = 0; i < bCnt; i++) blocks[i] = 0;
	}
	void update(int idx, int value) {
		int bIdx = idx / bSize;
		blocks[bIdx] -= arr[idx];
		arr[idx] = value;
		blocks[bIdx] += arr[idx];
	}
	int query(int left, int right) {
		int ret = 0;
		int s = left / bSize, e = min(right / bSize, n - 1);
		if (s == e) {
			for (int i = left; i <= right; i++) ret += arr[i];
			return ret;
		}
		for (int i = left; i <= (s + 1) * bSize - 1; i++) ret += arr[i];
		for (int i = s + 1; i <= e - 1; i++) ret += blocks[i];
		for (int i = e * bSize; i <= right; i++) ret += arr[i];
		return ret;
	}
};

Partition C, R;
Road roads[MAX_CITIES - 1];
priority_queue<Road> Q;

void init(int N, int mPopulation[]) {
	while (!Q.empty()) Q.pop();
	C.init(N); R.init(N - 1);
	for (int i = 0; i < N; i++) {
		cities[i] = mPopulation[i];
		C.update(i, cities[i]);
	}
	for (int i = 0; i < N - 1; i++) {
		Q.push(roads[i] = { i, cities[i + 1] + cities[i], 1 });
		R.update(i, roads[i].time);
	}
	return;
}

int expand(int M) {
	int rId, cnt = 0;
	while (!Q.empty() && cnt < M) {
		rId = Q.top().rId; Q.pop();
		roads[rId].expand();
		Q.push(roads[rId]);
		R.update(rId, roads[rId].time);
		cnt++;
	}
	return roads[rId].time;
}

int calculate(int mFrom, int mTo) {
	if (mFrom > mTo) {
		int temp = mFrom;
		mFrom = mTo;
		mTo = temp;
	}
	return R.query(mFrom, mTo - 1);
}

bool exeed(int mid, int K, int mFrom, int mTo) {
	int cnt = 1, sum = 0;
	for (int i = mFrom; i <= mTo; i++) {
		sum += cities[i];
		if (sum > mid) {
			sum = cities[i];
			cnt++;
			if (cnt > K) return true;
		}
	}
	return false;
}

int divide(int mFrom, int mTo, int K) {
	int start = cities[mFrom];
	int end = C.query(mFrom, mTo);
	int ret = end;
	while (start <= end) {
		int mid = (start + end) / 2;
		if (exeed(mid, K, mFrom, mTo)) start = mid + 1;
		else {
			ret = mid; end = mid - 1;
		}
	}
	return ret;
}
```
