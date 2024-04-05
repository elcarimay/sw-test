```cpp
#include <iostream>
using namespace std;

#define MAX_N 200000
#define INF 987654321

inline int max(int a, int b) { return a < b ? b : a; }
inline int min(int a, int b) { return a > b ? b : a; }
inline int ceil(int a, int b) { return (a + b - 1) / b; }

struct  Pair
{
	int idx, value;
};

struct Block
{
	int sum;
	Pair max, min;
};

struct Partition
{
	int N, bSize, bCnt, arr[MAX_N];
	Block blocks[MAX_N];
	void init(int _N) {
		N = _N; bSize = sqrt(N); bCnt = ceil(N, bSize);
		for (int i = 0;i < N;i++) arr[i] = 0;
		for (int i = 0;i < bCnt;i++) blocks[i] = { 0, {-1, -INF}, {-1, INF} };
	}
	void update(int idx, int value) {
		int bIdx = idx / bSize;
		blocks[bIdx].sum += value;
		arr[idx] += value;
		int left = bIdx * bSize;
		int right = min((bIdx + 1) * bSize - 1, N - 1);
		if (idx == blocks[bIdx].max.idx) {
			blocks[bIdx].max.value = -INF;
			for (int i = left;i <= right;i++)
				if (blocks[bIdx].max.value < arr[i])
					blocks[bIdx].max = { i, arr[i] };
		}
		else if (blocks[bIdx].max.value < arr[idx])
			blocks[bIdx].max = { idx, arr[idx] };
		if (idx == blocks[bIdx].min.idx) {
			blocks[bIdx].min.value = INF;
			for (int i = left;i <= right;i++)
				if (blocks[bIdx].min.value > arr[i])
					blocks[bIdx].min = { i, arr[i] };
		}
		else if (blocks[bIdx].min.value > arr[idx])
			blocks[bIdx].min = { idx, arr[idx] };
	}
	int querySum(int left, int right){
		int ret = 0;
		int s = left / bSize, e = right / bSize;
		if (s == e) {
			for (int i = left;i <= right;i++) ret += arr[i];
			return ret;
		}
		for (int i = left;i <= (s + 1) * bSize - 1;i++) ret += arr[i];
		for (int i = s + 1;i <= e - 1;i++) ret += blocks[i].sum;
		for (int i = e * bSize;i <= right;i++) ret += arr[i];
		return ret;
	}
	int queryMaxMin(int left, int right) {
		int resMax = -INF, resMin = INF;
		int s = left / bSize, e = right / bSize;
		if (s == e) {
			for (int i = left;i <= right;i++) {
				resMax = max(resMax, arr[i]);
				resMin = min(resMin, arr[i]);
			}
			return resMax - resMin;
		}
		for (int i = left;i <= (s + 1) * bSize - 1;i++) {
			resMax = max(resMax, arr[i]);
			resMin = min(resMin, arr[i]);
		}
		for (int i = s+1;i <= e-1;i++) {
			resMax = max(resMax, blocks[i].max.value);
			resMin = min(resMin, blocks[i].min.value);
		}
		for (int i = e*bSize;i <= right;i++) {
			resMax = max(resMax, arr[i]);
			resMin = min(resMin, arr[i]);
		}
		return resMax - resMin;
	}
}P;

void init(int N, int mSubscriber[]) {
	P.init(N);
	for (int i = 0;i < N;i++) P.update(i, mSubscriber[i]);
}

int subscribe(int mId, int mNum) {
	P.update(mId - 1, mNum);
	return P.arr[mId - 1];
}

int unsubscribe(int mId, int mNum) {
	P.update(mId - 1, -mNum);
	return P.arr[mId - 1];
}

int count(int sId, int eId) {
	return P.querySum(sId - 1, eId - 1);
}

int calculate(int sId, int eId) {
	return P.queryMaxMin(sId - 1, eId - 1);
}
```
