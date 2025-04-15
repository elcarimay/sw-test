```cpp
#include <queue>
#include <unordered_map>
#include <unordered_set>
#include <vector>
using namespace std;

struct Product {
    int id, category, company, price;
};

struct HeapItem {
    int price, id;
    bool operator<(const HeapItem& rhs) const {
        if (price != rhs.price) return price > rhs.price;  // 최소 힙
        return id > rhs.id;
    }
};

struct RESULT {
    int cnt;
    int IDs[5];
};

unordered_map<int, Product> productMap;
unordered_map<int, bool> isActive;
unordered_map<int, unordered_set<int>> byCategory;
unordered_map<int, unordered_set<int>> byCompany;
unordered_map<int, unordered_set<int>> byCatComp;
int discountCache[6][6];

priority_queue<HeapItem> pq;

int catCompKey(int cat, int comp) {
    return cat * 10 + comp;
}

int getRealPrice(const Product& p) {
    return p.price - discountCache[p.category][p.company];
}

void init() {
    productMap.clear();
    isActive.clear();
    pq = priority_queue<HeapItem>();
    for (int i = 1; i <= 5; ++i) {
        byCategory[i].clear();
        byCompany[i].clear();
        for (int j = 1; j <= 5; ++j) {
            discountCache[i][j] = 0;
            byCatComp[i * 10 + j].clear();
        }
    }
}

int sell(int mID, int mCategory, int mCompany, int mPrice) {
    Product p = { mID, mCategory, mCompany, mPrice };
    productMap[mID] = p;
    isActive[mID] = true;
    int realPrice = getRealPrice(p);
    pq.push({ realPrice, mID });

    byCategory[mCategory].insert(mID);
    byCompany[mCompany].insert(mID);
    byCatComp[catCompKey(mCategory, mCompany)].insert(mID);

    return byCatComp[catCompKey(mCategory, mCompany)].size();
}

int closeSale(int mID) {
    if (!isActive[mID]) return -1;
    Product p = productMap[mID];
    isActive[mID] = false;
    byCategory[p.category].erase(mID);
    byCompany[p.company].erase(mID);
    byCatComp[catCompKey(p.category, p.company)].erase(mID);
    return getRealPrice(p);
}

int discount(int mCategory, int mCompany, int mAmount) {
    int key = catCompKey(mCategory, mCompany);
    discountCache[mCategory][mCompany] += mAmount;

    vector<int> toRemove;
    for (int id : byCatComp[key]) {
        if (!isActive[id]) continue;
        Product& p = productMap[id];
        int newPrice = getRealPrice(p);
        if (newPrice <= 0) {
            isActive[id] = false;
            toRemove.push_back(id);
        }
        else {
            pq.push({ newPrice, id });
        }
    }

    for (int id : toRemove) {
        Product& p = productMap[id];
        byCategory[p.category].erase(id);
        byCompany[p.company].erase(id);
        byCatComp[key].erase(id);
    }

    return byCatComp[key].size();
}

RESULT show(int mHow, int mCode) {
    RESULT result = { 0 };
    int count = 0;

    unordered_set<int>* filter = nullptr;
    if (mHow == 1) filter = &byCategory[mCode];
    else if (mHow == 2) filter = &byCompany[mCode];

    vector<HeapItem> temp;

    while (!pq.empty() && count < 5) {
        HeapItem item = pq.top(); pq.pop();
        if (!isActive[item.id]) continue;

        Product& p = productMap[item.id];
        int realPrice = getRealPrice(p);

        if (realPrice != item.price) {
            pq.push({ realPrice, item.id });
            continue;
        }

        if (filter && !filter->count(item.id)) continue;

        result.IDs[count++] = item.id;
        temp.push_back(item);
    }

    for (auto& item : temp) pq.push(item);
    result.cnt = count;
    return result;
}
```
