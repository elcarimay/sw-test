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
int N, population[MAXN], sumA[103];
struct Road {
	int num, time;
};
Road roads[MAXN];
int sq;

void build() {
	for (sq = 1; sq * sq < N; sq++);
	for (int i = 0; i < N; i++) {
		if (i % sq == 0) sumA[i / sq] = 0;
		sumA[i / sq] += roads[i].time;
	}
}

void update(int idx, int value) {
	sumA[idx / sq] -= roads[idx].time;
	roads[idx].time = value;
	sumA[idx / sq] += value;
}

int sum_query(int l, int r) {
	int sumv = 0, s = l / sq, e = r / sq;
	if (s == e) {
		for (int i = l; i <= r; i++) sumv += roads[i].time;
		return sumv;
	}
	for (int i = l; i <= (s + 1) * sq - 1; i++) sumv += roads[i].time;
	for (int i = s + 1; i <= e - 1; i++) sumv += sumA[i];
	for (int i = e * sq; i <= r; i++) sumv += roads[i].time;
	return sumv;
}



void init(int N, int mPopulation[]) {
	::N = N, pq = {};
	memset(population, 0, sizeof(population));
	for (int i = 0; i < N; i++) population[i] = mPopulation[i];
	for (int i = 0; i < N - 1; i++) {
		roads[i].num = 1;
		pq.push({ roads[i].time = (population[i] + population[i + 1]), i });
	}
	build();
}

int expand(int M) {
	while (!pq.empty() && M > 0) {
		Data cur = pq.top(); pq.pop();
		int roadId = cur.roadId;
		roads[roadId].num += 1;
		int tmp = (population[roadId] + population[roadId + 1]) / roads[roadId].num;
		update(roadId, tmp);
		pq.push({ roads[roadId].time, roadId });
		if (--M == 0) return roads[roadId].time;
	}
}

int ret;
int calculate(int mFrom, int mTo) {
	if (mFrom > mTo) swap(mFrom, mTo);
	return sum_query(mFrom, mTo - 1);
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
