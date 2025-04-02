```cpp
#if 1
// segment ver.  175 ms
// 각 수명 index에 미생물 하나를 더하고 미생물이 추가되는 시점과 반감기를 더해서 시간에 따른 이벤트를 우큐로 처리
#include <vector>
#include <queue>
using namespace std;

#define MAXN 30003
#define MAXLIFE 1000003
struct Bacteria {
	int lifeSpan, halfTime;
}bac[MAXN];

int N, sumTree[MAXLIFE * 4];

void update(int node, int s, int e, int lifeVal, int val) {
	if (e < lifeVal || lifeVal < s) return;
	if (s == lifeVal && e == lifeVal) {
		sumTree[node] += val; return;
	}
	int mid = (s + e) / 2;
	int lnode = node * 2;
	int rnode = lnode + 1;
	if (lifeVal <= mid)	update(lnode, s, mid, lifeVal, val);
	else update(rnode, mid + 1, e, lifeVal, val);
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

struct Time {
	int id, time;
	bool operator<(const Time& r)const {
		return time > r.time;
	}
};
priority_queue<Time> pq;
void init() {
	pq = {}, N = 0;
	for (int i = 1; i < MAXN; i++) bac[i] = {};
	for (int i = 0; i < MAXLIFE * 4; i++) sumTree[i] = 0;
}

void update(int time) {
	while (!pq.empty() && pq.top().time <= time) {
		Time cur = pq.top(); pq.pop();
		auto& b = bac[cur.id].lifeSpan;
		update(1, 1, MAXLIFE, b, -1);
		b /= 2;
		if (b < 100) continue;
		update(1, 1, MAXLIFE, b, 1);
		pq.push({ cur.id, cur.time + bac[cur.id].halfTime });
	}
}

void addBacteria(int tStamp, int mID, int mLifeSpan, int mHalfTime) { // 30,000
	N = mID;
	bac[mID] = { mLifeSpan, mHalfTime };
	update(1, 1, MAXLIFE, mLifeSpan, 1);
	pq.push({ mID, tStamp + mHalfTime });
}

int getMinLifeSpan(int tStamp) { // 1,000
	update(tStamp);
	int minLife = MAXLIFE, retId = -1;
	for (int i = 1; i <= N; i++) {
		if (bac[i].lifeSpan < 100) continue;
		if (minLife > bac[i].lifeSpan) minLife = bac[i].lifeSpan, retId = i;
	}
	return retId;
}

int getCount(int tStamp, int mMinSpan, int mMaxSpan) { // 15,000
	update(tStamp);
	qs = mMinSpan, qe = mMaxSpan;
	int ret = sum_query(1, 1, MAXLIFE);
	return ret;
}
#endif // 1
```
