```cpp
#if 1
// O(N^3) 아주 느린 버전 → 전체 탐색(Brute Force)
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;
#define INF 1000003

struct Part {
	int pos, price, performance;
};
vector<Part> part[3]; // [mPosition] : price, performane
int parts[3][2];

struct Result {
	int mPrice, mPerformance;
};

int charge;
void init(int mCharge) {
	charge = mCharge;
	for (int i = 0; i < 3; i++) {
		part[i].clear();
		for (int j = 0; j < 2; j++) parts[i][j] = 0;
	}
}

int stock(int mType, int mPrice, int mPerformance, int mPosition) {
	part[mType].push_back({mPosition, mPrice, mPerformance});
	parts[mType][mPosition]++;
	return parts[mType][mPosition];
}

int condition(int mid) {
	int price = INF;
	for (auto i : part[0]) for (auto j : part[1]) for (auto k : part[2]) {
		int price_temp = i.price + j.price + k.price;
		int pos = i.pos + j.pos + k.pos;
		if (pos != 0 && pos != 3) price_temp += charge;
		int performance = min(min(i.performance, j.performance), k.performance);
		if (mid <= performance) price = min(price, price_temp);
	}
	return price;
}

Result order(int mBudget) {
	Result res = { 0, 0 };
	int s = 1, e = INF;
	while (s <= e) {
		int mid = (s + e) / 2;
		int cost = condition(mid);
		if (cost <= mBudget) s = mid + 1, res = { cost, mid };
		else e = mid - 1;
	}
	return res;
}
#endif // 1
```
