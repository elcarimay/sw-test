```cpp
#if 1
#include <unordered_map>
#include <set>
#include <vector>
#include <queue>
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
vector<int> v[6][6];

int getID(int c) {
    return pMap.count(c) ? pMap[c] : pMap[c] = pMap.size() + 1;
}
struct Info {
    int id, amount;
};
vector<Info> info;
void init() {
    pMap.clear(), info.clear();
    for (int i = 1; i <= 5; i++) for (int j = 1; j <= 5; j++)
        CatComp[i][j] = {}, v[i][j].clear();
}

int sell(int mID, int mCategory, int mCompany, int mPrice) {
    int id = getID(mID);
    product[id] = { mID, mCategory, mCompany, mPrice };
    CatComp[mCategory][mCompany].push({ id, mPrice });
    v[mCategory][mCompany].push_back(id);
    return (int)v[mCategory][mCompany].size();
}

int closeSale(int mID) {
    if (!pMap.count(mID)) return -1;
    int id = getID(mID);
    if (product[id].removed) return -1;
    product[id].removed = true;
    auto& tmp = v[product[id].category][product[id].company];
    tmp.erase(find(tmp.begin(), tmp.end(), id));
    return product[id].price;
}

int discount(int mCategory, int mCompany, int mAmount) {
    for (Info nx : info) nx.amount += mAmount;
    info.push_back({ (int)pMap.size(), mAmount });





    auto& c_tmp = CatComp[mCategory][mCompany];
    auto& v_tmp = v[mCategory][mCompany];
    for (int i = 0; i < v_tmp.size(); i++) {
        int id = v_tmp[i];
        product[id].price -= mAmount;
        if (product[id].price <= 0) {
            product[id].removed = true;
            v_tmp.erase(v_tmp.begin() + i--);
        }
        c_tmp.push({ id, product[id].price });
    }
    return (int)v_tmp.size();
}

priority_queue<Data> pq, final;
RESULT show(int mHow, int mCode) {
    RESULT result = { 0 }; pq = {}, final = {};
    int cnt;
    if (!mHow) {
        for (int i = 1; i <= 5; i++) for (int j = 1; j <= 5; j++) {
            cnt = 0;
            while (!CatComp[i][j].empty()) {
                auto cur = CatComp[i][j].top(); CatComp[i][j].pop();
                if (product[cur.id].price <= 0) continue;
                if (product[cur.id].removed) continue;
                if (product[cur.id].price != cur.price) continue;
                pq.push(cur), final.push(cur);
                if (++cnt == 5) break;
            }
            while (!pq.empty()) CatComp[i][j].push(pq.top()), pq.pop();
        }
    }
    else if (mHow == 1) {
        for (int j = 1; j <= 5; j++) {
            cnt = 0;
            while (!CatComp[mCode][j].empty()) {
                auto cur = CatComp[mCode][j].top(); CatComp[mCode][j].pop();
                if (product[cur.id].price <= 0) continue;
                if (product[cur.id].removed) continue;
                if (product[cur.id].price != cur.price) continue;
                pq.push(cur), final.push(cur);
                if (++cnt == 5) break;
            }
            while (!pq.empty()) CatComp[mCode][j].push(pq.top()), pq.pop();
        }
    }
    else if (mHow == 2) {
        for (int i = 1; i <= 5; i++) {
            cnt = 0;
            while (!CatComp[i][mCode].empty()) {
                auto cur = CatComp[i][mCode].top(); CatComp[i][mCode].pop();
                if (product[cur.id].price <= 0) continue;
                if (product[cur.id].removed) continue;
                if (product[cur.id].price != cur.price) continue;
                pq.push(cur), final.push(cur);
                if (++cnt == 5) break;
            }
            while (!pq.empty()) CatComp[i][mCode].push(pq.top()), pq.pop();
        }
    }
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
