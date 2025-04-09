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
int N, population[MAXN];
struct Road {
	int num, time;
};
Road roads[MAXN];
void init(int N, int mPopulation[]) {
	::N = N, pq = {};
	for (int i = 0; i < N; i++) population[i] = mPopulation[i];
	for (int i = 0; i < N - 1; i++) {
		roads[i].num = 1;
		pq.push({ roads[i].time = (population[i] + population[i + 1]), i });
	}
}

int expand(int M) {
	while (!pq.empty() && M > 0) {
		Data cur = pq.top(); pq.pop();
		int roadId = cur.roadId;
		roads[roadId].num += 1;
		roads[roadId].time = (population[roadId] + population[roadId + 1]) / roads[roadId].num;
		pq.push({ roads[roadId].time, roadId });
		if (--M == 0) return roads[roadId].time;
	}
}

int ret;
int calculate(int mFrom, int mTo) {
	ret = 0;
	if (mFrom > mTo) swap(mFrom, mTo);
	for (int i = mFrom; i < mTo; i++) ret += roads[i].time;
	return ret;
}

int from, to;
bool exceed(int mid, int K) {
	int sum = 0, cnt = 1;
	for (int i = from; i <= to; i++){
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
