```cpp
#include <vector>
using namespace std;

#define MAX_ROADS 100001
#define MAX_TYPES 1000

inline int ceil(int a, int b) { return (a + b - 1) / b; }

int roads[MAX_ROADS];
vector<int> roadList[MAX_TYPES];

struct Partition
{
	int n, bSize, bCnt, arr[MAX_ROADS], blocks[MAX_ROADS];
	void init(int num_roads) {
		n = num_roads; bSize = sqrt(n); bCnt = ceil(n, bSize);
		for (int i = 0; i < n; i++) arr[i] = 0;
		for (int i = 0; i < bCnt; i++) blocks[i] = 0;
	}
	void update(int idx, int value) {
		int bIdx = idx / bSize;
		blocks[bIdx] -= arr[idx];
		arr[idx] = value;
		blocks[bIdx] += arr[idx];
	}
	int queryRange(int left, int right) {
		int ret = 0;
		int s = left / bSize, e = right / bSize;
		if (s == e) {
			for (int i = left; i <= right; i++) ret += arr[i];
			return ret;
		}
		for (int i = left; i <= (s + 1) * bSize - 1; i++) ret += arr[i];
		for (int i = s + 1; i <= e - 1; i++) ret += blocks[i];
		for (int i = e*bSize; i <= right; i++) ret += arr[i];
		return ret;
	}
}P;

void init(int N, int M, int mType[], int mTime[]) {
	for (int i = 0; i < M; i++) roadList[i].clear();
	P.init(N - 1);
	for (int i = 0; i < N - 1; i++) {
		roads[i] = mTime[i];
		roadList[mType[i]].push_back(i);
		P.update(i, mTime[i]);
	}
}

void destroy() {}

void update(int mID, int mNewTime) {
	P.update(mID, roads[mID] = mNewTime);
}

int updateByType(int mTypeID, int mRatio256) {
	int ret = 0;
	for (int rIdx : roadList[mTypeID]) {
		P.update(rIdx, roads[rIdx] = roads[rIdx] * mRatio256 / 256);
		ret += roads[rIdx];
	}
	return ret;
}

int calculate(int mA, int mB) {
	if (mA > mB) swap(mA, mB);
	return P.queryRange(mA, mB - 1);
}
```
