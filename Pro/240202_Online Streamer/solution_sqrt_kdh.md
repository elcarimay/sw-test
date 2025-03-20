```cpp
#include <vector>
#include <algorithm>
using namespace std;

#define MAXN 500
#define INF 987654321

int* A;
int N, sumA[MAXN], maxA[MAXN], minA[MAXN], sq;
void build(int a[]) {
	for (sq = 1;sq * sq < N;sq++);
	for (int i = 0; i < N;i++) {
		int bid = i / sq;
		if (i % sq == 0) sumA[bid] = 0, maxA[bid] = -INF, minA[bid] = INF;
		maxA[bid] = max(maxA[bid], A[i]);
		minA[bid] = min(minA[bid], A[i]);
		sumA[bid] += A[i];
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
	sumA[bid] += val;
}

int sum_query(int l, int r) {
	int sumv = 0;
	while (l <= r && l % sq) sumv += A[l++];
	while (l <= r && (r + 1) % sq) sumv += A[r--];
	while (l <= r) sumv += sumA[l / sq], l += sq;
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
	::N = N, A = mSubscriber;
	build(A);
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
