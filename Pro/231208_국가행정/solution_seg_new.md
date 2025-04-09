```cpp
#include <string.h>
#include <algorithm>
#include <queue>
using namespace std;
#define MAXN 10003
#define INF 100000000

struct Data {
	int time, roadId;
	bool operator<(const Data& r)const {
		if (time != r.time) return time < r.time;
		return roadId > r.roadId;
	}
};
priority_queue<Data> pq;
int N, population[MAXN], sumTree[MAXN * 4];
struct Road {
	int num, time;
};
Road roads[MAXN];

void build(int node, int s, int e) {
	if (s == e) {
		sumTree[node] = roads[s].time; return;
	}
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	build(lnode, s, mid); build(rnode, mid + 1, e);
	sumTree[node] = sumTree[lnode] + sumTree[rnode];
}

void update(int node, int s, int e, int idx, int value) {
	if (s == idx && e == idx) {
		sumTree[node] += value; return;
	}
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	if(idx <= mid) update(lnode, s, mid, idx, value);
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

void init(int N, int mPopulation[]) {
	::N = N, pq = {};
	memset(population, 0, sizeof(population));
	for (int i = 0; i < N; i++) population[i] = mPopulation[i];
	for (int i = 0; i < N - 1; i++) {
		roads[i].num = 1;
		pq.push({ roads[i].time = (population[i] + population[i + 1]), i });
	}
	build(1, 0, N - 1);
}

int expand(int M) {
	while (!pq.empty() && M > 0) {
		Data cur = pq.top(); pq.pop();
		int roadId = cur.roadId;
		roads[roadId].num += 1;
		update(1, 0, N - 1, roadId, -roads[roadId].time);
		roads[roadId].time = (population[roadId] + population[roadId + 1]) / roads[roadId].num;
		update(1, 0, N - 1, roadId, roads[roadId].time);
		pq.push({ roads[roadId].time, roadId });
		if (--M == 0) return roads[roadId].time;
	}
}

int ret;
int calculate(int mFrom, int mTo) {
	if (mFrom > mTo) swap(mFrom, mTo);
	qs = mFrom, qe = mTo - 1;
	int ret = sum_query(1, 0, N - 1);
	return ret;
}

int from, to;
bool exceed(int mid, int K) {
	int sum = 0, cnt = 1;
	for (int i = from; i <= to; i++) {
		sum += population[i];
		if (sum > mid) {
			sum = population[i];
			if (++cnt > K) return true;
		}
	}
	return false;
}

int divide(int mFrom, int mTo, int K) {
	from = mFrom, to = mTo;
	int s = 1, e = INF, ret;
	while (s <= e) {
		int mid = (s + e) / 2;
		if (exceed(mid, K)) s = mid + 1;
		else e = mid - 1, ret = mid;
	}
	return ret;
}
```
