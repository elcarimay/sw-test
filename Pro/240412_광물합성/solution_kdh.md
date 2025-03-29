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
int shipFee, type[2][3];
void init(int mShipFee) {
    shipFee = mShipFee;
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++)
        v[i][j].clear(), type[i][j] = 0;
}

int gather(int mMineId, int mType, int mCost, int mContent) {
    v[mMineId][mType].push_back({ mCost, mContent });
    return ++type[mMineId][mType];
}

int condition(int mid) {
    int minPrice[2][3];
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++) minPrice[i][j] = INF;
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++)
        for (Result nx : v[i][j]) if (nx.mContent >= mid) minPrice[i][j] = min(minPrice[i][j], nx.mCost);
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
        int ret = condition(mid);
        if (ret <= mBudget) s = mid + 1, res = {ret, bestmid = mid };
        else e = mid - 1;
    }
    int minPrice[2][3];
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++) minPrice[i][j] = INF;
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++)
        for (Result nx : v[i][j]) if (nx.mContent >= bestmid) minPrice[i][j] = min(minPrice[i][j], nx.mCost);
    int p1, p2, p3;
    for (int i = 0; i < 2; i++) for (int j = 0; j < 2; j++) for (int k = 0; k < 2; k++)
        p1 = minPrice[i][0], p2 = minPrice[j][1], p3 = minPrice[k][2];
    for (int i = 0; i < 2; i++) for (int j = 0; j < 3; j++) {
        for (int k = 0; k < v[i][j].size();k++) {
            if ((v[i][j][k].mContent >= bestmid) && ((j == 0 && p1 == v[i][j][k].mCost) ||
                (j == 1 && p2 == v[i][j][k].mCost) || (j == 2 && p3 == v[i][j][k].mCost)))
                v[i][j].erase(v[i][j].begin() + k);
        }
    }  
    return res;
}
#endif // 1

```
