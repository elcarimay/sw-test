```cpp
#if 1
#include <cstdio>
#include <vector>
#include <cmath>
using namespace std;

#define MAX_NODES	1000000
#define INF			10000

int min(int a, int b) { return a < b ? a : b; }
int max(int a, int b) { return a < b ? b : a; }

struct Result {
	int top, count;
};

struct Data {
	int top, bot, base;
	long long sum;
};

struct Partition
{
	int arr[MAX_NODES], bSize, bCnt, N;
	Data buckets[MAX_NODES];
	void init(int num_values) {
		N = num_values;
		bSize = sqrt(N);
		bCnt = ceil((double)N / bSize);
		for (int i = 0; i < N; i++) arr[i] = 0;
		for (int i = 0; i < bCnt; i++) buckets[i] = {};
	}
	void update_minmax(int bIdx) {
		int left = bIdx * bSize;
		int right = min((bIdx + 1) * bSize - 1, N - 1);
		buckets[bIdx].top = 0;
		buckets[bIdx].bot = INF;
		for (int i = left; i <= right; i++)
		{
			buckets[bIdx].top = max(buckets[bIdx].top, query(i));
			buckets[bIdx].bot = min(buckets[bIdx].bot, query(i));
		}
	}
	void update(int left, int right, int value) {
		int s = left / bSize, e = right / bSize;
		if (s == e) {
			for (int i = left; i <= right; i++) {
				arr[i] += value;
				buckets[s].sum += value;
			}
			update_minmax(s);
			return;
		}
		for (int i = left; i < (s + 1) * bSize; i++) {
			arr[i] += value; buckets[s].sum += value;
		}
		update_minmax(s);
		for (int i = s + 1; i <= (e - 1); i++) {
			buckets[i].top += value;
			buckets[i].bot += value;
			buckets[i].base += value;
			buckets[i].sum += value * bSize;
		}
		for (int i = e * bSize; i <= right ; i++) {
			arr[i] += value; buckets[e].sum += value;
		}
		update_minmax(e);
	}
	int query(int idx) {
		return arr[idx] + buckets[idx / bSize].base;
	}
	Data query() { 
		Data res = { 0,INF, 0, 0 };
		for (int i = 0; i < bCnt; i++)
		{
			res.top = max(res.top, buckets[i].top);
			res.bot = min(res.bot, buckets[i].bot);
			res.sum += buckets[i].sum;
		}
		return res;
	}
}part;

// C: 격자판의 열의 개수 (10 ≤ C ≤ 1,000,000)
void init(int C) {
	part.init(C);
}

// 3,000
Result dropBlocks(int mCol, int mHeight, int mLength) {
	Result ret = { 0,0 };
	part.update(mCol, mCol + mLength - 1, mHeight);
	Data data = part.query();

	ret.top = data.top - data.bot;
	ret.count = (data.sum - (long long)part.N * data.bot) % 1000000;
	return ret;
}
#endif
```
