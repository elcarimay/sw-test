```cpp
#if 1
// 성능이 mid 이상인 조합 중 가장 저렴한 가격을 계산하므로 미리 최소가격으로 계산함
// 그러면 3개 타입별로 2개 위치만 고려하면 되므로 O(2 * 2 * 2) = 8이 됨
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;
#define INF 1000003

struct Part {
	int price, performance;
};
vector<Part> part[2][3]; // [Position][type] : price, performane

struct Result {
	int mPrice, mPerformance;
};

int charge;
void init(int mCharge) {
	charge = mCharge;
	for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++) part[i][j].clear();
}

int stock(int mType, int mPrice, int mPerformance, int mPosition) {
	part[mPosition][mType].push_back({mPrice, mPerformance});
	return (int)part[mPosition][mType].size();
}

int condition(int mid) {
	int minPrice[2][3];
	fill(&minPrice[0][0], &minPrice[0][0] + 2 * 3, INF);
	for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++)
		for (auto& p : part[i][j])
			if (p.performance >= mid) minPrice[i][j] = min(minPrice[i][j], p.price);
	int res = INF;
	for (int i = 0; i < 2; i++) for (int j = 0; j < 2; j++) for (int k = 0; k < 2; k++) {
		int p1 = minPrice[i][0], p2 = minPrice[j][1], p3 = minPrice[k][2];
		if (p1 == INF || p2 == INF || p3 == INF) continue;
		int price = p1 + p2 + p3;
		int pos = i + j + k;
		if (pos != 0 && pos != 3) price += charge;
		res = min(res, price);
	}
	return res;
}

Result order(int mBudget) {
	Result res = {};
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
