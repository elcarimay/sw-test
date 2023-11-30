```cpp
#include <vector>
#include <queue>
using namespace std;

const int INF = 987'654'321;

struct info {
	int sCity, eCity, cost, time;
	info(int _s, int _e, int _c, int _t) : sCity(_s), eCity(_e), cost(_c), time(_t) {};
};

struct city {
	vector<info> mcost;
};
city cinfo[100];
int mintime;

queue <info> movelist;

void init(int N, int K, int sCity[], int eCity[], int mCost[], int mTime[]) {
	for (int i = 0; i < N; i++) cinfo[i].mcost.clear();
	for (int i = 0; i < K; i++) cinfo[sCity[i]].mcost.emplace_back(sCity[i], eCity[i], mCost[i], mTime[i]);
	return;
}

void add(int sCity, int eCity, int mCost, int mTime) {
	for (int i = 0; i < cinfo[sCity].mcost.size(); i++) {//갈수 있는 목적지가 동일한경우 저장하지 않음
		auto cur = cinfo[sCity].mcost.at(i);
		if (cur.eCity == eCity && cur.cost == mCost && cur.time == mTime) return;
	}
	cinfo[sCity].mcost.emplace_back(sCity, eCity, mCost, mTime);
	return;
}

void move() {
	auto next = movelist.front(); movelist.pop();

	if (mintime != INF && next.time > mintime) return; // 구해진 시간보다 시간이 안좋으면 리턴
	if (next.cost < 0) return; // 비용이 0보다 아래로 내려가면 리턴
	if (next.sCity == next.eCity) {
		mintime = mintime > next.time ? next.time : mintime;
		return; // 도착지이면 더 작은 시간 보관
	}
	for (int i = 0; i < cinfo[next.sCity].mcost.size(); i++) { // 못찾았을경우 큐에 집어 넣음
		auto old = cinfo[next.sCity].mcost.at(i);
		movelist.emplace(old.eCity, next.eCity, next.cost - old.cost, next.time + old.time);
	}
}

int cost(int M, int sCity, int eCity) {
	int result = -1;
	mintime = INF;
	while (movelist.size()) { movelist.pop(); }
	movelist.emplace(sCity, eCity, M, 0);
	while (movelist.size()) { move();}
	if (mintime != INF) result = mintime;
	return result;
}
```
