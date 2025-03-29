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
	part[mType].push_back({ mPosition, mPrice, mPerformance });
	parts[mType][mPosition]++;
	return parts[mType][mPosition];
}

int condition(int mid) {
    int minPrice[3][2];
    for (int i = 0; i < 3; i++) for (int j = 0; j < 2; j++) minPrice[i][j] = INF;
    for (int i = 0; i < 3; i++) for (auto& p : part[i]) 
        if (p.performance >= mid) minPrice[i][p.pos] = min(minPrice[i][p.pos], p.price);
    int res = INF;
    for (int i = 0; i < 2; i++) for (int j = 0; j < 2; j++) for (int k = 0; k < 2; k++) {
        int p1 = minPrice[0][i], p2 = minPrice[1][j], p3 = minPrice[2][k];
        if (p1 == INF || p2 == INF || p3 == INF) continue;
        int price = p1 + p2 + p3;
        int pos = i + j + k;
        if (pos != 0 && pos != 3) price += charge;
        res = min(res, price);
    }
    return res;
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
