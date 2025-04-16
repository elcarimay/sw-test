```cpp
#if 1
#include <unordered_map>
#include <unordered_set>
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
int sq = 1000; // MAX_price 1000000
#define MAXB 1010
unordered_map<int, int> pMap;
unordered_set<int> alive[6][6]; // 현재 살아있는 상품
priority_queue<Data> CatComp[6][6];
vector<int> block_price[6][6][MAXB]; // 가격 블록별 상품 저장

int getID(int c) {
    return pMap.count(c) ? pMap[c] : pMap[c] = pMap.size() + 1;
}
struct Info {
    int id, amount;
};
vector<Info> info;
void init() {
    pMap.clear(), info.clear();
    for (int i = 1; i <= 5; i++) for (int j = 1; j <= 5; j++) {
        CatComp[i][j] = {}, alive[i][j].clear();
        for (int k = 0; k < MAXB; k++) block_price[i][j][k].clear();
    }
}

int sell(int mID, int mCategory, int mCompany, int mPrice) {
    int id = getID(mID);
    product[id] = { mID, mCategory, mCompany, mPrice };
    CatComp[mCategory][mCompany].push({ id, mPrice });
    alive[mCategory][mCompany].insert(id);
    block_price[mCategory][mCompany][mPrice / sq].push_back(id); // 추가
    return (int)alive[mCategory][mCompany].size();
}

int closeSale(int mID) {
    if (!pMap.count(mID)) return -1;
    int id = getID(mID);
    if (product[id].removed) return -1;
    product[id].removed = true;
    alive[product[id].category][product[id].company].erase(id);
    return product[id].price;
}

int discount(int mCategory, int mCompany, int mAmount) {
    auto& aset = alive[mCategory][mCompany];
    int cnt = 0;
    vector<int> toRemove;

    // 가격 블록 순회
    for (int b = 0; b < MAXB; ++b) {
        auto& ids = block_price[mCategory][mCompany][b];
        vector<int> remain;

        for (int id : ids) {
            if (product[id].removed) continue;
            product[id].price -= mAmount;
            if (product[id].price <= 0) {
                product[id].removed = true;
                aset.erase(id);
            }
            else {
                int newBlock = product[id].price / sq;
                if (newBlock == b) {
                    remain.push_back(id); // same block
                }
                else {
                    // move to new block
                    block_price[mCategory][mCompany][newBlock].push_back(id);
                }
            }
        }
        ids = remain; // update current block with remaining ones
    }

    return aset.size();
}

priority_queue<Data> pq, final;
int cnt;
void push_valid(int cat, int com) {
    cnt = 0;
    while (!CatComp[cat][com].empty()) {
        auto cur = CatComp[cat][com].top(); CatComp[cat][com].pop();
        if (product[cur.id].price <= 0) continue;
        if (product[cur.id].removed) continue;
        if (product[cur.id].price != cur.price) continue;
        pq.push(cur), final.push(cur);
        if (++cnt == 5) break;
    }
    while (!pq.empty()) CatComp[cat][com].push(pq.top()), pq.pop();
}

RESULT show(int mHow, int mCode) {
    RESULT result = { 0 }; pq = {}, final = {};
    if (!mHow) for (int i = 1; i <= 5; i++) for (int j = 1; j <= 5; j++)
        push_valid(i, j);
    else if (mHow == 1) for (int j = 1; j <= 5; j++) push_valid(mCode, j);
    else for (int i = 1; i <= 5; i++) push_valid(i, mCode); //  mHow == 2
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
