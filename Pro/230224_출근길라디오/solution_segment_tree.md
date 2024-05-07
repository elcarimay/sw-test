```cpp
#if 1
#include <cstdio>
#include <vector>
#include <algorithm>
using namespace std;

#define MAX_ROADS 100005
#define MAX_TYPES 1005

int time[MAX_ROADS];
vector<int> type[MAX_TYPES];
int tree[4 * MAX_ROADS];

struct SegmentTree
{
	void init(int s, int e, int n) {
		if (s == e) {
			tree[n] = time[s]; return;
		}
		int m = (s + e) / 2;
		init(s, m, 2 * n);
		init(m + 1, e, 2 * n + 1);
		tree[n] = tree[2 * n] + tree[2 * n + 1];
		return;
	}
	void update(int s, int e, int n, int idx, int diff) {
		if (idx < s || e < idx) return;
		if (s == e) {
			tree[n] = diff; return;
		}
		int m = (s + e) / 2;
		update(s, m, 2 * n, idx, diff);
		update(m + 1, e, 2 * n + 1, idx, diff);
		tree[n] = tree[2 * n] + tree[2 * n + 1];
		return;
	}
	int sum_query(int s, int e, int n, int l, int r) {
		if (r < s || e < l) return 0;
		if (l <= s && e <= r) return tree[n];
		int m = (s + e) / 2;
		int sum_left = sum_query(s, m, 2 * n, l, r);
		int sum_right = sum_query(m + 1, e, 2 * n + 1, l, r);
		return sum_left + sum_right;
	}
}S;

int N;
void init(int N, int M, int mType[], int mTime[]) {
	::N = N;
	for (int i = 0; i < M; i++) type[i].clear();
	for (int i = 0; i < N - 1; i++) {
		type[mType[i]].push_back(i);
		time[i] = mTime[i];
	}
	S.init(0, N - 2, 1);
}

void destroy() {}

void update(int mID, int mNewTime) {
	S.update(0, N - 2, 1, mID, time[mID] = mNewTime);
}

int updateByType(int mTypeID, int mRatio256) {
	int ret = 0;
	for (auto t : type[mTypeID]) {
		S.update(0, N - 2, 1, t, time[t] = time[t] * mRatio256 / 256);
		ret += time[t];
	}
	return ret;
}

int calculate(int mA, int mB) {
	if (mA > mB) swap(mA, mB);
	return S.sum_query(0, N - 2, 1, mA, mB - 1);
}
#endif // 1
```
