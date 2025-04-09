```cpp
#if 1 // 160 ms
#include <queue>
using namespace std;

#define MAX_CITIES 10000
#define MAX_BLOCKS 101

inline int max(int a, int b) { return a < b ? b : a; }
inline int min(int a, int b) { return a > b ? b : a; }
inline int ceil(int a, int b) { return (a + b - 1) / b; }

int cities[MAX_CITIES], cTree[4 * MAX_CITIES];
int tTree[4 * MAX_CITIES];

struct Road
{
	int rId, time, numRoads;
	void expand() {
		time = (cities[rId + 1] + cities[rId]) / ++numRoads;
	}
	bool operator<(const Road& road)const {
		return (time < road.time) ||
			(time == road.time && rId > road.rId);
	}
}roads[MAX_CITIES - 1];

struct SegmentTree_City
{
	void init(int s, int e, int n) {
		if (s == e) { cTree[n] = cities[s]; return; }
		int m = (s + e) / 2;
		init(s, m, 2 * n);
		init(m + 1, e, 2 * n + 1);
		cTree[n] = cTree[2 * n] + cTree[2 * n + 1];
		return;
	}
	void update(int s, int e, int n, int idx, int value) {
		if (idx < s || e < idx) return;
		if (s == e) { cTree[n] += value; return; }
		int m = (s + e) / 2;
		update(s, m, 2 * n, idx, value);
		update(m + 1, e, 2 * n + 1, idx, value);
		cTree[n] = cTree[2 * n] + cTree[2 * n + 1];
		return;
	}
	int sum_query(int s, int e, int n, int l, int r) {
		if (r < s || e < l) return 0;
		if (l <= s && e <= r) return cTree[n];
		int m = (s + e) / 2;
		int left = sum_query(s, m, 2 * n, l, r);
		int right = sum_query(m + 1, e, 2 * n + 1, l, r);
		return left + right;
	}
}C;

struct SegmentTree_Time
{
	void init(int s, int e, int n) {
		if (s == e) { tTree[n] = roads[s].time; return; }
		int m = (s + e) / 2;
		init(s, m, 2 * n);
		init(m + 1, e, 2 * n + 1);
		tTree[n] = tTree[2 * n] + tTree[2 * n + 1];
		return;
	}
	void update(int s, int e, int n, int idx, int value) {
		if (idx < s || e < idx) return;
		if (s == e) { tTree[n] = value; return; }
		int m = (s + e) / 2;
		update(s, m, 2 * n, idx, value);
		update(m + 1, e, 2 * n + 1, idx, value);
		tTree[n] = tTree[2 * n] + tTree[2 * n + 1];
		return;
	}
	int sum_query(int s, int e, int n, int l, int r) {
		if (r < s || e < l) return 0;
		if (l <= s && e <= r) return tTree[n];
		int m = (s + e) / 2;
		int left = sum_query(s, m, 2 * n, l, r);
		int right = sum_query(m + 1, e, 2 * n + 1, l, r);
		return left + right;
	}
}T;

int N;
priority_queue<Road> Q;
void init(int N, int mPopulation[]) {
	::N = N;
	while (!Q.empty()) Q.pop();
	C.init(0, N - 1, 1); T.init(0, N - 2, 1);
	for (int i = 0; i < N; i++) {
		cities[i] = 0; roads[i] = {};
		C.update(0, N - 1, 1, i, cities[i] = mPopulation[i]);
	}
	for (int i = 0; i < N - 1; i++) {
		Q.push(roads[i] = { i, cities[i + 1] + cities[i], 1 });
		T.update(0, N - 2, 1, i, roads[i].time);
	}
}

int expand(int M) {
	int rId, cnt = 0;
	while (!Q.empty() && cnt < M) {
		rId = Q.top().rId; Q.pop();
		roads[rId].expand();
		Q.push(roads[rId]);
		T.update(0, N - 2, 1, rId, roads[rId].time);
		cnt++;
	}
	return roads[rId].time;
}

int calculate(int mFrom, int mTo) {
	if (mFrom > mTo) swap(mFrom, mTo);
	return T.sum_query(0, N - 2, 1, mFrom, mTo - 1);
}

bool exeed(int mid, int K, int mFrom, int mTo) {
	int cnt = 1, sum = 0;
	for (int i = mFrom; i <= mTo; i++)
	{
		sum += cities[i];
		if (sum > mid) {
			sum = cities[i];
			if (++cnt > K) return true;
		}
	}
	return false;
}

int divide(int mFrom, int mTo, int K) {
	int start = cities[mFrom], end = C.sum_query(0, N - 1, 1, mFrom, mTo);
	int ret = end;
	while (start <= end) {
		int mid = (start + end) / 2;
		if (exeed(mid, K, mFrom, mTo)) start = mid + 1;
		else {
			end = mid - 1; ret = mid;
		}
	}
	return ret;
}

#endif // 1

```
