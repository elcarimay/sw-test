```cpp
#include <cstdio>
#include <vector>
#include <queue>
using namespace std;

const int INF = 1e6;

struct Result {
    int mCost;
    int mContent;
    bool operator<(const Result& r)const {
        return (mContent < r.mContent) ||
            (mContent == r.mContent && mCost > r.mCost);
    }
    bool operator==(const Result& r)const {
        return (mContent == r.mContent && mCost == r.mCost);
    }
};

int fee;
vector<Result> v[2][3];
Result r[2][3];

void init(int mShipFee) {
    fee = mShipFee;
    for (int i = 0;i < 2;i++) for (int j = 0;j < 3;j++) {
        v[i][j].clear(); r[i][j] = {};
    }
}

int gather(int mMineId, int mType, int mCost, int mContent) {
    v[mMineId][mType].push_back({ mCost, mContent });
    return v[mMineId][mType].size();
}

Result mix(int mBudget) {
    Result res = {0,0};
    int Content, s = 1, e = INF, mid = INF, m0 = INF, m1 = INF, mt = INF, cost = INF;
    int cost_copy = 0, m0_copy = 0, m1_copy = 0, mt_copy = 0;
    Result r_copy[2][3];
    while (s <= e) {
        mid = (s + e) / 2;
        for (int i = 0;i < 2;i++)
            for (int j = 0;j < 3;j++) {
                r[i][j] = { INF,0 };
                for (auto n : v[i][j]) {
                    if (n.mContent >= mid)
                        if (r[i][j].mCost > n.mCost) r[i][j] = n;
                }
            }

        m0 = r[0][0].mCost + r[0][1].mCost + r[0][2].mCost + fee;
        m1 = r[1][0].mCost + r[1][1].mCost + r[1][2].mCost + fee;
        mt = min(r[0][0].mCost, r[1][0].mCost) +
            min(r[0][1].mCost, r[1][1].mCost) +
            min(r[0][2].mCost, r[1][2].mCost) + fee * 2;
        cost = min(min(m0, m1), mt);

        if (cost <= mBudget) {
            cost_copy = 0, m0_copy = 0, m1_copy = 0, mt_copy = 0;
            s = mid + 1; res = { cost, mid };
            for (int i = 0;i < 2;i++) for (int j = 0;j < 3;j++)
                r_copy[i][j] = r[i][j];
            cost_copy = cost, m0_copy = m0, m1_copy = m1, mt_copy = mt;
        }
        else {
            e = mid - 1;
        }
    }
    if (cost_copy == m0_copy && cost_copy) {
        for (int i = 0;i < 3;i++)
            for (int j = 0;j < v[0][i].size();j++)
                if (v[0][i][j] == r_copy[0][i]) {
                    v[0][i].erase(v[0][i].begin() + j); break;
                }
    }
    else if (cost_copy == m1_copy && cost_copy) {
        for (int i = 0;i < 3;i++)
            for (int j = 0;j < v[1][i].size();j++)
                if (v[1][i][j] == r_copy[1][i]) {
                    v[1][i].erase(v[1][i].begin() + j); break;
                }
    }
    else if (cost_copy == mt_copy && cost_copy) {
        for (int i = 0;i < 3;i++) {
            int k = (r_copy[0][i].mCost < r_copy[1][i].mCost) ? 0 : 1;
            for (int j = 0;j < v[k][i].size();j++)
                if (v[k][i][j] == r_copy[k][i]) {
                    v[k][i].erase(v[k][i].begin() + j); break;
                }
        }
    }
    return res;
}
```
