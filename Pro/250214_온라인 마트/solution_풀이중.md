```cpp
#if 1
#include <unordered_map>
#include <unordered_set>
#include <set>
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

struct Product {
    int id, category, company, price;
    bool removed;
}product[50003];

struct RESULT {
    int cnt, IDs[5];
};

struct Data {
    int id, price;
    bool operator<(const Data& r) const {
        return price == r.price ? product[id].id > product[r.id].id : price > r.price;
    }
};
unordered_map<int, int> pMap;
priority_queue<Data> CatComp[6][6];

int getID(int c) {
    return pMap.count(c) ? pMap[c] : pMap[c] = pMap.size() + 1;
}

unordered_set<int> removeList[6][6];
struct Info {
    int id, amount;
};
vector<Info> v[6][6];
void init() {
    pMap.clear();
    for (int i = 1; i <= 5; i++) for (int j = 1; j <= 5; j++)
        CatComp[i][j] = {}, removeList[i][j].clear(), v[i][j].clear();
}

int sell(int mID, int mCategory, int mCompany, int mPrice) {
    int id = getID(mID);
    product[id] = { mID, mCategory, mCompany, mPrice };
    CatComp[mCategory][mCompany].push({ id, mPrice });
    return (int)CatComp[mCategory][mCompany].size();
}

int closeSale(int mID) {
    if (!pMap.count(mID)) return -1;
    int id = getID(mID);
    if (product[id].removed) return -1;
    product[id].removed = true;
    removeList[product[id].category][product[id].company].insert(id);
    return product[id].price;
}

int discount(int mCategory, int mCompany, int mAmount) {
    for (auto& nx : v[mCategory][mCompany]) nx.amount += mAmount;
    v[mCategory][mCompany].push_back({ (int)pMap.size(), mAmount });
    while (!CatComp[mCategory][mCompany].empty()) {
        auto cur = CatComp[mCategory][mCompany].top(); CatComp[mCategory][mCompany].pop();
        if (product[cur.id].price <= 0) continue;
        if (product[cur.id].removed) continue;
        //if (product[cur.id].price != cur.price) continue;
        if (cur.price <= mAmount) {
            if (removeList[mCategory][mCompany].count(cur.id)) removeList[mCategory][mCompany].erase(cur.id);
            continue;
        }
        if (cur.price > mAmount) break;
    }
    return (int)CatComp[mCategory][mCompany].size() - removeList[mCategory][mCompany].size();
}

priority_queue<Data> pq, final;
int cnt;
void push_valid(int cat, int com) {
    cnt = 0;
    while (!CatComp[cat][com].empty()) {
        auto cur = CatComp[cat][com].top(); CatComp[cat][com].pop();
        if (product[cur.id].price <= 0) continue;
        if (product[cur.id].removed) continue;
        //if (product[cur.id].price != cur.price) continue;
        auto it = lower_bound(v[cat][com].begin(), v[cat][com].end(), Info{ cur.id });
        cur.price -= it->amount;
        pq.push(cur); continue;
        final.push(cur);
        if (++cnt == 5) break;
    }
    while (!pq.empty()) CatComp[cat][com].push(pq.top()), pq.pop();
}

RESULT show(int mHow, int mCode) {
    RESULT result = { 0 }; pq = {}, final = {};
    int cnt;
    if (!mHow) for (int i = 1; i <= 5; i++) for (int j = 1; j <= 5; j++)
        push_valid(i, j);
    else if (mHow == 1)
        for (int j = 1; j <= 5; j++) push_valid(mCode, j);
    else //  mHow == 2
        for (int i = 1; i <= 5; i++) push_valid(i, mCode);
    cnt = 0;
    while (!final.empty()) {
        result.IDs[cnt] = product[final.top().id].id, final.pop();
        if (++cnt == 5) break;
    }
    result.cnt = cnt;
    return result;
}
#endif // 0
```
