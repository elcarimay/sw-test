```cpp
#include <vector>
#include <algorithm>
using namespace std;
#define INF 987654321
#define MAXN 500

int maxA[MAXN], minA[MAXN], sumTree[MAXN];
int N, sq;
int* A;
void build(int N, int mA[]) {
	::N = N, A = mA;
	for (sq = 1; sq * sq < N; sq++);
	for (int i = 0; i < N; i++) {
		int bid = i / sq;
		if (bid * sq == i) sumTree[bid] = 0, maxA[bid] = -INF, minA[bid] = INF;
		maxA[bid] = max(maxA[bid], A[i]);
		minA[bid] = min(minA[bid], A[i]);
		sumTree[bid] += A[i];
	}
}

void update(int x, int val) {
	int orgVal = A[x];
	A[x] += val;
	int bid = x / sq;
	int l = bid * sq;
	int r = min(l + sq, N);

	if (val < 0) {
		if (maxA[bid] == orgVal) maxA[bid] = *max_element(A + l, A + r);
		minA[bid] = min(minA[bid], A[x]);
	}
	else {
		maxA[bid] = max(maxA[bid], A[x]);
		if (minA[bid] == orgVal) minA[bid] = *min_element(A + l, A + r);
	}
	sumTree[bid] += val;
}

int sum_query(int l, int r) {
	int sumv = 0;
	while (l <= r && l % sq) sumv += A[l++];
	while (l <= r && (r + 1) % sq) sumv += A[r--];
	while (l <= r) sumv += sumTree[l / sq], l += sq;
	return sumv;
}

int maxmin_query(int l, int r) {
	int maxv = -INF, minv = INF;
	while (l <= r && l % sq) maxv = max(maxv, A[l]), minv = min(minv, A[l++]);
	while (l <= r && (r + 1) % sq) maxv = max(maxv, A[r]), minv = min(minv, A[r--]);
	while (l <= r) maxv = max(maxv, maxA[l / sq]), minv = min(minv, minA[l / sq]), l += sq;
	return maxv - minv;
}

void init(int N, int mSubscriber[]) {
	build(N, mSubscriber);
}

int subscribe(int mId, int mNum) {
	update(mId - 1, mNum);
	return A[mId - 1];
}

int unsubscribe(int mId, int mNum) {
	subscribe(mId, -mNum);
	return A[mId - 1];
}

int count(int sId, int eId) {
	return sum_query(sId - 1, eId - 1);
}

int calculate(int sId, int eId) {
	return maxmin_query(sId - 1, eId - 1);
}
```
