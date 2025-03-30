```cpp
#if 1
#include <vector>
#include <algorithm>
using namespace std;

#define INF 1e6

struct Result {
    int mCost, mContent;
};

vector<Result> v[2][3];
int shipFee;
void init(int mShipFee) {
    shipFee = mShipFee;
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++)
        v[i][j].clear();
}

int gather(int mMineId, int mType, int mCost, int mContent) {
    v[mMineId][mType].push_back({ mCost, mContent });
    return (int)v[mMineId][mType].size();
}

int minPrice[2][3];
int condition(int mid) {
    fill(&minPrice[0][0], &minPrice[0][0] + 2 * 3, INF);
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++) for (Result nx : v[i][j])
        if (nx.mContent >= mid) minPrice[i][j] = min(minPrice[i][j], nx.mCost);
    int ret = INF;
    for (int i = 0; i < 2; i++) for (int j = 0; j < 2; j++) for (int k = 0; k < 2; k++) {
        int p1 = minPrice[i][0], p2 = minPrice[j][1], p3 = minPrice[k][2];
        if (p1 == INF || p2 == INF || p3 == INF) continue;
        int mine = i + j + k;
        int cost = p1 + p2 + p3;
        if (mine != 0 && mine != 3) cost += shipFee * 2;
        else cost += shipFee;
        ret = min(ret, cost);
    }
    return ret;
}

void erase(int mineId, int typeId, int content) {
    auto& vec = v[mineId][typeId];
    int minPrice = INF, indexToDelete = -1;
    for (int i = 0; i < vec.size(); i++)
        if (vec[i].mContent >= content && vec[i].mCost < minPrice)
            minPrice = vec[indexToDelete = i].mCost;
    if (indexToDelete != -1) vec.erase(vec.begin() + indexToDelete);
}

Result mix(int mBudget) {
    Result res = { 0,0 };
    int s = 0, e = INF, Content = 0;
    while (s <= e) {
        int mid = (s + e) / 2;
        int cost = condition(mid);
        if (cost <= mBudget) s = mid + 1, res = { cost, Content = mid };
        else e = mid - 1;
    }
    if (!res.mCost) return res;
    fill(&minPrice[0][0], &minPrice[0][0] + 2 * 3, INF);
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++) for (Result nx : v[i][j])
        if (nx.mContent >= Content) minPrice[i][j] = min(minPrice[i][j], nx.mCost);
    for (int i = 0; i < 2; i++) for (int j = 0; j < 2; j++) for (int k = 0; k < 2; k++) {
        int p1 = minPrice[i][0], p2 = minPrice[j][1], p3 = minPrice[k][2];
        if (p1 == INF || p2 == INF || p3 == INF) continue;
        int cost = p1 + p2 + p3;
        int mine = i + j + k;
        if (mine != 0 && mine != 3) cost += (shipFee * 2);
        else cost += shipFee;
        if (cost == res.mCost)
            erase(i, 0, Content), erase(j, 1, Content), erase(k, 2, Content);
    }
    return res;
}
#endif // 1
```
