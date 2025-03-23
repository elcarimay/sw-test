```cpp
#if 1 // segmentTree ver. 489 ms
#include <vector>
using namespace std;

#define MAXN 100003
#define MAX_TYPES 1003

int N;
int* A;
int sumTree[4 * MAXN];
void build(int node, int s, int e) {
	if (s == e) {
		sumTree[node] = A[s]; return;
	}
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	build(lnode, s, mid);
	build(rnode, mid + 1, e);
	sumTree[node] = sumTree[lnode] + sumTree[rnode];
}

void update(int node, int s, int e, int idx, int value) {
	if (s == e) {
		sumTree[node] = value; return;
	}
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	if (idx <= mid) update(lnode, s, mid, idx, value);
	else update(rnode, mid + 1, e, idx, value);
	sumTree[node] = sumTree[lnode] + sumTree[rnode];
}
int qs, qe;
int sum_query(int node, int s, int e) {
	if (qe < s || e < qs) return 0;
	if (qs <= s && e <= qe) return sumTree[node];
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	return sum_query(lnode, s, mid) + sum_query(rnode, mid + 1, e);
}

vector<int> roadType[MAX_TYPES];
void init(int N, int M, int mType[], int mTime[]) {
	::N = N, A = mTime;
	for (int i = 0; i < M; i++) roadType[i].clear();
	for (int i = 0; i < N - 1; i++)	roadType[mType[i]].push_back(i);
	build(1, 0, N - 2);
}

void destroy() {}

void update(int mID, int mNewTime) {
	A[mID] = mNewTime;
	update(1, 0, N - 2, mID, mNewTime);
}

int updateByType(int mTypeID, int mRatio256) {
	int ret = 0;
	for (auto nx : roadType[mTypeID]) {
		A[nx] = (int)(A[nx] * mRatio256 / 256);
		update(1, 0, N - 2, nx, A[nx]);
		ret += A[nx];
	}
	return ret;
}

int calculate(int mA, int mB) {
	if (mA > mB) swap(mA, mB);
	qs = mA, qe = mB - 1;
	return sum_query(1, 0, N - 2);
}
#endif
```
