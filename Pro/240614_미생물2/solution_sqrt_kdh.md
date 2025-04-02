```cpp
#if 1
// sqrt ver. 132 ms
// 각 수명 index에 미생물 하나를 더하고 미생물이 추가되는 시점과 반감기를 더해서 시간에 따른 이벤트를 우큐로 처리
#include <vector>
#include <queue>
using namespace std;

#define MAXN 30003
#define MAXLIFE 1000003
#define BUCKETSIZE 1100
struct Bacteria {
	int lifeSpan, halfTime;
}bac[MAXN];

int sq, N, A[MAXLIFE], sumA[BUCKETSIZE];

void build() {
	for (sq = 1; sq * sq < MAXLIFE; sq++);
}

void update(int lifeVal, int value) {
	A[lifeVal] += value, sumA[lifeVal / sq] += value;
}

int sum_query(int l, int r) {
	int sumv = 0;
	/*while (l <= r && l % sq) sumv += A[l++];
	while (l <= r && (r + 1) % sq) sumv += A[r--];
	while (l <= r) sumv += sumA[l / sq], l += sq;*/
	int sbid = l / sq, ebid = r / sq;
	if (sbid == ebid){
		for (int i = l; i <= r; i++) sumv += A[i];
		return sumv;
	}
	for (int i = l; i <= (sbid + 1) * sq - 1; i++) sumv += A[i];
	for (int i = ebid * sq; i <= r; i++) sumv += A[i];
	for (int i = sbid + 1; i <= ebid - 1; i++) sumv += sumA[i];
	return sumv;
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
	for (int i = 1; i < MAXLIFE; i++) A[i] = 0;
	for (int i = 0; i < BUCKETSIZE; i++) sumA[i] = 0;
	build();
}

void update(int time) {
	while (!pq.empty() && pq.top().time <= time) {
		Time cur = pq.top(); pq.pop();
		auto& b = bac[cur.id].lifeSpan;
		update(b, -1);
		b /= 2;
		if (b < 100) continue;
		update(b, 1);
		pq.push({ cur.id, cur.time + bac[cur.id].halfTime});
	}
}

void addBacteria(int tStamp, int mID, int mLifeSpan, int mHalfTime) { // 30,000
	N = mID;
	bac[mID] = { mLifeSpan, mHalfTime };
	update(mLifeSpan, 1);
	pq.push({ mID, tStamp + mHalfTime });
}

int getMinLifeSpan(int tStamp) { // 1,000
	update(tStamp);
	int minLife = 1e6 + 3, retId = -1;
	for (int i = 1; i <= N; i++) {
		if (bac[i].lifeSpan < 100) continue;
		if (minLife > bac[i].lifeSpan) minLife = bac[i].lifeSpan, retId = i;
	}
	return retId;
}

int getCount(int tStamp, int mMinSpan, int mMaxSpan) { // 15,000
	update(tStamp);
	return sum_query(mMinSpan, mMaxSpan);
}
#endif // 1
```
