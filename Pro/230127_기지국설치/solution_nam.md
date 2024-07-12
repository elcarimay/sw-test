```cpp
#include <algorithm>
#include <vector>
#include <unordered_map>
using namespace std;

#define MAX_BUILDINGS (100 + 24000)
#define ADDED 0
#define REMOVED 1

struct Building {
	int mLocation, state;
}buildings[MAX_BUILDINGS];
int addCnt, removeCnt;
unordered_map<int, int> bMap;

int get_Index(int mid) {
	int bidx;
	if (bMap.find(mid) == bMap.end()) {
		bidx = addCnt++;
		bMap.insert({ mid, bidx });
	}
	else {
		bidx = bMap.find(mid)->second;
		if (buildings[bidx].state == REMOVED) {
			buildings[bidx].state = ADDED;
			removeCnt--;
		}
	}
	return bidx;
}

void init(int N, int mId[], int mLocation[]) {
	addCnt = removeCnt = 0;
	bMap.clear();
	for (int i = 0; i < N; i++) {
		int bidx = get_Index(mId[i]);
		buildings[bidx] = { mLocation[i], ADDED };
	}
}

int add(int mId, int mLocation) {
	int bidx = get_Index(mId);
	buildings[bidx] = { mLocation, ADDED };
	return addCnt - removeCnt;
}

int remove(int mStart, int mEnd) {
	for (int i = 0; i < addCnt; i++) {
		if (buildings[i].state == REMOVED) continue;
		if (mStart <= buildings[i].mLocation && buildings[i].mLocation <= mEnd) {
			buildings[i].state = REMOVED;
			removeCnt++;
		}
	}
	return addCnt - removeCnt;
}

vector<int> locations;

// [결정문제] 기지국 사이 가장 인접한 거리가 x일 때, 기지국의 개수는 M 이상인가?
bool condition(int x, int M) {
	int cnt = 1;
	int cur = locations[0];
	for (int i = 0; i < addCnt - removeCnt; i++)
		if (locations[i] - cur >= x) {
			cnt++;
			cur = locations[i];
		}
	return cnt >= M;
}

int search(int low, int high, int M) {
	int sol = low;
	while (low <= high) {
		int mid = (high + low) / 2;
		if (condition(mid, M)) sol = mid, low = mid + 1;
		else high = mid - 1;
	}
	return sol;
}

int install(int M) {
	locations.clear();
	for (int i = 0; i < addCnt; i++) {
		if (buildings[i].state == REMOVED) continue;
		locations.push_back(buildings[i].mLocation);
	}
	sort(locations.begin(), locations.end());
	int low = 0;
	int high = locations[addCnt - removeCnt - 1] - locations[0];
	return search(low, high, M);
}
```
