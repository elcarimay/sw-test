```cpp
#include <vector>
#include <algorithm>
using namespace std;
#define INF 987654321
#define MAXN 1 << 19

int maxTree[MAXN], minTree[MAXN], sumTree[MAXN];
int N;
int* A;

void build(int node, int s, int e) {
	if (s == e) {
		maxTree[node] = minTree[node] = sumTree[node] = A[s]; return;
	}
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	build(lnode, s, mid); build(rnode, mid + 1, e);
	maxTree[node] = max(maxTree[lnode], maxTree[rnode]);
	minTree[node] = min(minTree[lnode], minTree[rnode]);
	sumTree[node] = sumTree[lnode] + sumTree[rnode];
}

void update(int node, int s, int e, int idx) {
	if (s == e) {
		maxTree[node] = minTree[node] = sumTree[node] = A[s]; return;
	}
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	if(idx <= mid) update(lnode, s, mid, idx);
	else update(rnode, mid + 1, e, idx);
	maxTree[node] = max(maxTree[lnode], maxTree[rnode]);
	minTree[node] = min(minTree[lnode], minTree[rnode]);
	sumTree[node] = sumTree[lnode] + sumTree[rnode];
}

int max_query(int node, int s, int e, int l, int r) {
	if (e < l || r < s) return -INF;
	if (l <= s && e <= r) return maxTree[node];
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	return max(max_query(lnode, s, mid, l, r), max_query(rnode, mid + 1, e, l, r));
}

int min_query(int node, int s, int e, int l, int r) {
	if (e < l || r < s) return INF;
	if (l <= s && e <= r) return minTree[node];
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	return min(min_query(lnode, s, mid, l, r), min_query(rnode, mid + 1, e, l, r));
}

int sum_query(int node, int s, int e, int l, int r) {
	if (e < l || r < s) return 0;
	if (l <= s && e <= r) return sumTree[node];
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	return sum_query(lnode, s, mid, l, r) + sum_query(rnode, mid + 1, e, l, r);
}

void init(int N, int mSubscriber[]) {
	::N = N, A = mSubscriber;
	build(1, 0, N - 1);
}

int subscribe(int mId, int mNum) {
	A[mId - 1] += mNum;
	update(1, 0, N - 1, mId - 1);
	return A[mId - 1];
}

int unsubscribe(int mId, int mNum) {
	subscribe(mId, -mNum);
	return A[mId - 1];
}

int count(int sId, int eId) {
	return sum_query(1, 0, N - 1, sId - 1, eId - 1);
}

int calculate(int sId, int eId) {
	return max_query(1, 0, N - 1, sId - 1, eId - 1) - min_query(1, 0, N - 1, sId - 1, eId - 1);
}
```
