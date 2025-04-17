```cpp
#if 1
#include<vector>
#include<unordered_map>
#include<queue>
using namespace std;
#define	MAX_CITY	300
#define	INF	100'000'000
struct ROAD {
	int id, to, dist;
	bool isRemoved;
};
struct DATA {
	int toN, cost, minCost;
	bool operator<(const DATA& d)const {
		return this->cost > d.cost;
	}
};
unordered_map<int, int> chargeCostInCity;
unordered_map<int, ROAD> roadInfo;
vector<ROAD> cityAdj[MAX_CITY];
int _N;
void add(int mId, int sCity, int eCity, int mDistance) {
	roadInfo.insert({ mId, {mId, eCity, mDistance, false} });
	cityAdj[sCity].push_back({ mId, eCity, mDistance, false });
}

void init(int N, int mCost[], int K, int mId[], int sCity[], int eCity[], int mDistance[]) {
	_N = N, chargeCostInCity.clear(), roadInfo.clear();
	for (int i = 0; i < _N; i++) cityAdj[i].clear(), chargeCostInCity[i] = mCost[i];
	for (int i = 0; i < K; i++) add(mId[i], sCity[i], eCity[i], mDistance[i]);
}

void remove(int mId) {
	roadInfo[mId].isRemoved = true;
}

int cost(int sCity, int eCity) {
	vector<pair<int, int>> Dijkstra(_N, { INF, INF });
	priority_queue<DATA> dataQ;
	dataQ.push({ sCity, 0, chargeCostInCity[sCity] });
	while (dataQ.size()) {
		DATA cur = dataQ.top(); dataQ.pop();
		if (cur.toN == eCity) return cur.cost;
		for (auto& next : cityAdj[cur.toN]) {
			int nextCost = next.dist * cur.minCost + cur.cost;
			if ((Dijkstra[next.to].first < nextCost && Dijkstra[next.to].second <= cur.minCost) || roadInfo[next.id].isRemoved)
				continue;
			int nextMinCost = min(chargeCostInCity[next.to], cur.minCost);
			dataQ.push({ next.to, nextCost, nextMinCost });
			Dijkstra[next.to] = { nextCost, nextMinCost };
		}
	}
	return -1;
}
#endif // 1
```
