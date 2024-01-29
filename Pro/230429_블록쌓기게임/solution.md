```cpp
#if 1
#include <cstdio>
#include <vector>
#include <cmath>
using namespace std;

#define INF 10000

int min(int a, int b) { return a < b ? a : b; }
int max(int a, int b) { return a < b ? b : a; }

struct Result {
	int top, count;
};

int num_values, num_buckets, bucket_size;

struct Data
{
	int top, bot, base;
	long long sum;
};

struct Partition
{
	vector<Data> buckets;
	vector<int> values;

	int N; // bucket size

	void init() {
		N = bucket_size;
		buckets.clear(); buckets.resize(num_buckets);
		values.clear(); values.resize(num_values);
	}
	void update_minmax(int bIdx) {
		int left = bIdx * N;
		int right = min((bIdx + 1) * N - 1, num_values - 1);
		buckets[bIdx].top = 0;
		buckets[bIdx].bot = INF;
		for (int i = left; i <= right; i++)
		{
			buckets[bIdx].top = max(buckets[bIdx].top, query(i));
			buckets[bIdx].bot = min(buckets[bIdx].bot, query(i));
		}
	}
	void update(int left, int right, int value) {
		int s = left / N; int e = right / N;
		if (s == e) {
			for (int i = left; i <= right; i++)
			{
				values[i] += value;
				buckets[s].sum += value;
			}
			update_minmax(s);
			return;
		}
		while (left / N == s) {
			values[left++] += value;
			buckets[s].sum += value;
		}
		update_minmax(s);
		while (right / N == e) {
			values[right--] += value;
			buckets[e].sum += value;
		}
		update_minmax(e);
		for (int i = s + 1; i <= e - 1; i++)
		{
			buckets[i].top += value;
			buckets[i].bot += value;
			buckets[i].base += value;
			buckets[i].sum += value * N;
		}
	}
	int query(int idx) {
		return values[idx] + buckets[idx / N].base;
	}
	Data query() {
		Data res = { 0, INF, 0, 0 };
		for (int i = 0; i < num_buckets; i++)
		{
			res.top = max(res.top, buckets[i].top);
			res.bot = min(res.bot, buckets[i].bot);
			res.sum += buckets[i].sum;
		}
		return res;
	}
} part;

// C: 격자판의 열의 개수 (10 ≤ C ≤ 1,000,000)
void init(int C){
	bucket_size = sqrt(C);
	num_values = C;
	num_buckets = ceil((double)num_values / bucket_size);
	part.init();
}

// 3,000
Result dropBlocks(int mCol, int mHeight, int mLength){
	Result ret = {0,0};

	part.update(mCol, mCol + mLength - 1, mHeight);
	Data data = part.query();

	ret.top = data.top - data.bot;
	ret.count = (data.sum - (long long)num_values * data.bot) % 1000000;
	return ret;
}
#endif
```
