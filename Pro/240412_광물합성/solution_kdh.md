```cpp
#if 1
#include <vector>
#include <algorithm>
using namespace std;

#define INF 1e6

struct Result {
    int mCost, mContent;
}res;

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
    fill(&minPrice[0][0], &minPrice[0][0], INF);
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++) for (Result nx : v[i][j])
        if (nx.mContent >= mid) minPrice[i][j] = min(minPrice[i][j], nx.mCost);
    int res = INF;
    for (int i = 0; i < 2; i++) for (int j = 0; j < 2; j++) for (int k = 0; k < 2; k++) {
        int p1 = minPrice[i][0], p2 = minPrice[j][1], p3 = minPrice[k][2];
        if (p1 == INF || p2 == INF || p3 == INF) continue;
        int mine = i + j + k;
        int cost = p1 + p2 + p3;
        if (mine != 0 || mine != 3) cost += shipFee * 2;
        else cost += shipFee;
        res = min(res, cost);
    }
    return res;
}

Result mix(int mBudget) {
    res = { 0,0 };
    int s = 0, e = INF, bestmid;
    while (s <= e) {
        int mid = (s + e) / 2;
        int cost = condition(mid);
        if (cost <= mBudget) s = mid + 1, res = { cost, bestmid = mid };
        else e = mid - 1;
    }
    fill(&minPrice[0][0], &minPrice[0][0], INF);
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++) for (Result nx : v[i][j])
        if (nx.mContent >= bestmid) minPrice[i][j] = min(minPrice[i][j], nx.mCost);
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++)
        for (vector<Result>::iterator it = v[i][j].begin(); it != v[i][j].end();) {
            if ((it->mContent >= bestmid) && (j == 0 && it->mCost == minPrice[i][j]) &&
                (j == 0 && it->mCost == minPrice[i][j]) && (j == 0 && it->mCost == minPrice[i][j]))
                it = v[i][j].erase(it);
            else it++;
        }
    return res;
}
#endif // 1
```
