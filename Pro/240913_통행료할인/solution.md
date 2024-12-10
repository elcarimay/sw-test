```cpp
#include<queue>
#include<unordered_map>
#include<queue>

#define MAX_CITY 300
#define MAX_DISCOUNT_TICKET 11
using namespace std;

struct RoadInfo{
	int id, sCity, eCity, mToll;
};

struct CityInfo{
	int id;
	vector<RoadInfo> outRoad;
};

struct PathCost{
	int city, used, cost;
};

unordered_map<int, RoadInfo> roads;
CityInfo cities[MAX_CITY];

void AddRoad(int mid, int sCity, int eCity, int mToll){
	roads[mid] = { mid, sCity, eCity, mToll };
	cities[sCity].outRoad.push_back(roads[mid]);
}

void RemoveRoad(int mId){
	int currentCity = roads[mId].sCity;
	for (int i = 0; i < cities[currentCity].outRoad.size(); ++i)
		if (cities[currentCity].outRoad[i].id == mId) {
			cities[currentCity].outRoad.erase(cities[currentCity].outRoad.begin() + i);
			break;
		}
	roads.erase(mId);
}

void init(int N, int K, int mId[], int sCity[], int eCity[], int mToll[]) {
	for (int i = 0; i < MAX_CITY; ++i) {
		cities[i].id = i;
		cities[i].outRoad.clear();
	}
	roads.clear();
	for (int i = 0; i < K; ++i) AddRoad(mId[i], sCity[i], eCity[i], mToll[i]);
}

void add(int mId, int sCity, int eCity, int mToll) {
	AddRoad(mId, sCity, eCity, mToll);
}

void remove(int mId) {
	RemoveRoad(mId);
}

int cost(int M, int sCity, int eCity) {
	int visit[MAX_CITY][MAX_DISCOUNT_TICKET];
	for (int i = 0; i < MAX_CITY; ++i) for (int j = 0; j < MAX_DISCOUNT_TICKET; j++)
		visit[i][j] = INT_MAX;
	queue<PathCost> qPath;
	PathCost currentPath, nextPath;
	visit[sCity][0] = 0;
	currentPath = {sCity, 0, 0};
	qPath.push(currentPath);
	while (!qPath.empty()) {
		currentPath = qPath.front(); qPath.pop();
		for (int i = 0; i < cities[currentPath.city].outRoad.size(); ++i) {
			nextPath.city = cities[currentPath.city].outRoad[i].eCity;
			nextPath.cost = currentPath.cost + cities[currentPath.city].outRoad[i].mToll;
			nextPath.used = currentPath.used;
			if (nextPath.cost < visit[nextPath.city][currentPath.used]) {
				visit[nextPath.city][currentPath.used] = nextPath.cost;
				qPath.push(nextPath);
			}
			nextPath.city = cities[currentPath.city].outRoad[i].eCity;
			nextPath.cost = currentPath.cost + cities[currentPath.city].outRoad[i].mToll / 2;
			nextPath.used = currentPath.used + 1;
			if (nextPath.cost < visit[nextPath.city][nextPath.used] && nextPath.used <= M) {
				visit[nextPath.city][nextPath.used] = nextPath.cost;
				qPath.push(nextPath);
			}
		}
	}
	int ret = INT_MAX;
	for (int i = 0; i < MAX_DISCOUNT_TICKET; ++i) {
		if (ret > visit[eCity][i]) {
			ret = visit[eCity][i];
		}
	}
	if (ret == INT_MAX) ret = -1;
	return ret;
}
```
