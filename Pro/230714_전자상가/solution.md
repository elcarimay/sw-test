```cpp
struct Result {
    int mPrice;
    int mPerformance;
};

#include<vector>
#include<algorithm>
using namespace std;
const int INF = 1e6; // 주어진 한계가 있기때문에 987654321로 하면 안됨

int charge;             // 운송비
vector<Result> A[2][3];    // A[w][t] = w창고, t타입 부품 리스트 {price, performance}

void init(int mCharge) {
    charge = mCharge;
    for (int i = 0; i < 2; i++)
        for (int j = 0; j < 3; j++)
            A[i][j].clear();
}

Result order(int mBudget) {
    int s = 1, e = INF;        // 성능
    Result res = {};
    while (s <= e) {
        int mid = (s + e) / 2;

        // P[w][t] = (w창고, t타입 부품) 중 mid성능 이상인 부품 최소비용
        int P[2][3] = {};
        for (int i = 0; i < 2; i++)
            for (int j = 0; j < 3; j++) {
                P[i][j] = INF;
                for (auto nx : A[i][j]) {
                    int price = nx.mPrice, performance = nx.mPerformance;
                    if (nx.mPerformance >= mid) P[i][j] = min(P[i][j], nx.mPrice);
                }
            }

        // 0번 창고 최소비용
        int a = P[0][0] + P[0][1] + P[0][2];

        // 1번 창고 최소비용
        int b = P[1][0] + P[1][1] + P[1][2];

        // 0번 + 1번 창고 최소비용
        int c = charge + min(P[0][0], P[1][0]) + min(P[0][1], P[1][1]) + min(P[0][2], P[1][2]);

        int price = min(min(a, b), c);

        // 예산 내에 mid 성능 이상 구매 가능한 경우
        if (price <= mBudget) {
            s = mid + 1;
            res = { price, mid }; // 마지막에 들어올때가 성능이 가장 좋은 경우이다.
        }

        // 불가능한 경우
        else e = mid - 1;
    }

    return res;
}

int stock(int mType, int mPrice, int mPerformance, int mPosition) {
    A[mPosition][mType].push_back({ mPrice, mPerformance });
    return A[mPosition][mType].size();
}
```
