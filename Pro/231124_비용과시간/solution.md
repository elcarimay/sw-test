```cpp
#include <queue>
using namespace std;

#define MAX_CITY 100

struct Road {
	int cost, time, destination;
	bool operator<(const Road& road) const {
		return this->time > road.time;
	}
};

struct City {
	vector<Road> roadInfo;
};

City cityInfo[MAX_CITY];

void init(int N, int K, int sCity[], int eCity[], int mCost[], int mTime[]) {
	for (int i = 0; i < MAX_CITY; i++) cityInfo[i].roadInfo.clear();
	for (int i = 0; i < K; i++) cityInfo[sCity[i]].roadInfo.push_back({ mCost[i], mTime[i], eCity[i]});
	return;
}

void add(int sCity, int eCity, int mCost, int mTime) {
	cityInfo[sCity].roadInfo.push_back({ mCost, mTime, eCity });
	return;
}

int cost(int M, int sCity, int eCity) {
	priority_queue<Road> dataQ;
	dataQ.push({ 0, 0, sCity });

	while (dataQ.size()) {
		Road cur_data = dataQ.top(); dataQ.pop();
		if (M - cur_data.cost < 0) continue;
		if (cur_data.destination == eCity) return cur_data.time;

		for (auto road : cityInfo[cur_data.destination].roadInfo)
			dataQ.push({ road.cost + cur_data.cost, road.time + cur_data.time, road.destination });
	}
	return -1;
}
```
