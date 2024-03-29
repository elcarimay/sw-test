```cpp
#include <cstdio>
#include <vector>
using namespace std;

#define MAX_NODES 1000000 // 격자판 열의 개수
#define INF 10000

inline int min(int a, int b) { return a < b ? a : b; }
inline int max(int a, int b) { return a < b ? b : a; }
inline int ceil(int a, int b) { return (a + b - 1) / b; }

struct Result
{
	int top, count;
};

struct Block
{
	int top, bot, base;
	long long sum;
};

struct Partition
{
	int N, bSize, bCnt, arr[MAX_NODES];
	Block blocks[MAX_NODES];
	void init(int _N) {
		N = _N;
		bSize = sqrt(N);
		bCnt = ceil(N, bSize);
		for (int i = 0; i < N; i++) arr[i] = 0;
		for (int i = 0; i < bCnt; i++) blocks[i] = {};
	}
	void update_minmax(int bIdx) {
		int left = bIdx * bSize;
		int right = min((bIdx + 1) * bSize - 1, N - 1);
		blocks[bIdx].top = 0;
		blocks[bIdx].bot = INF;
		for (int i = left; i <= right; i++)
		{
			blocks[bIdx].top = max(blocks[bIdx].top, arr[i] + blocks[bIdx].base);
			blocks[bIdx].bot = min(blocks[bIdx].bot, arr[i] + blocks[bIdx].base);
		}
	}
	void update(int left, int right, int value) {
		int s = left / bSize, e = right / bSize;
		if (s == e) {
			for (int i = left; i <= right; i++) {
				arr[i] += value; blocks[s].sum += value;
			}
			update_minmax(s);
			return;
		}
		for (int i = left; i <= (s + 1) * bSize - 1; i++) {
			arr[i] += value; blocks[s].sum += value;
		}
		update_minmax(s);
		for (int i = s + 1; i <= e - 1; i++) {
			blocks[i].top += value;
			blocks[i].bot += value;
			blocks[i].base += value;
			blocks[i].sum += value * bSize;
		}
		for (int i = e * bSize; i <= right; i++) {
			arr[i] += value; blocks[e].sum += value;
		}
		update_minmax(e);
	}
	Block query() {
		Block res = { 0, INF, 0, 0 };
		for (int i = 0; i < bCnt; i++)
		{
			res.top = max(res.top, blocks[i].top);
			res.bot = min(res.bot, blocks[i].bot);
			res.sum += blocks[i].sum;
		}
		return res;
	}
}P;

void init(int C) {
	P.init(C);
}

Result dropBlocks(int mCol, int mHeight, int mLength) {
	Result ret = { 0,0 };
	P.update(mCol, mCol + mLength - 1, mHeight);
	Block data = P.query();
	ret.top = data.top - data.bot;
	ret.count = (data.sum - (long long)P.N * data.bot) % 1000000;
	return ret;
}
```
