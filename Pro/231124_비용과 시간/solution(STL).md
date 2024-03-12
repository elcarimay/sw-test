```cpp
#include <vector>
#include <queue>
using namespace std;

#define MAX_CITY 100

int mTime[MAX_CITY];

struct City
{
	int to, cost, time;
	bool operator<(const City& data)const {
		return time > data.time;
	}
};

vector<City> cities[MAX_CITY];

void init(int N, int K, int sCity[], int eCity[], int mCost[], int mTime[]) {
	for (int i = 0; i < MAX_CITY; i++) cities[i].clear();
	for (int i = 0; i < K; i++)
		cities[sCity[i]].push_back({ eCity[i], mCost[i], mTime[i] });
	return;
}

void add(int sCity, int eCity, int mCost, int mTime) {
	cities[sCity].push_back({ eCity, mCost, mTime });
	return;
}

int cost(int M, int sCity, int eCity) {
	priority_queue<City> PQ;
	PQ.push({ sCity, 0, 0 });
	while (!PQ.empty()) {
		auto cur = PQ.top(); PQ.pop();
		if (M - cur.cost < 0) continue;
		if (cur.to == eCity) return cur.time;
		for (auto nx : cities[cur.to])
			PQ.push({ nx.to, nx.cost + cur.cost, nx.time + cur.time });
	}
	return -1;
}
```
