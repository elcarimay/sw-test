```cpp
#include <unordered_map>
#include <vector>
#include <algorithm>
using namespace std;

#define MAXN 16 // 최대 상품 수 15 + 1

struct RESULT { int cnt, IDs[5]; };

unordered_map<int, int> hmap;
struct Info {
    int mid, cat, com, price;
    bool removed;
};
vector<Info> info;
int idCnt;

struct Data {
    int id;
    bool operator<(const Data& r) const {
        if (info[id].price != info[r.id].price) return info[id].price < info[r.id].price;
        return info[id].mid < info[r.id].mid;
    }
};
vector<Data> catcom[3][3]; // mCategory, mCompany = 1~2

int getID(int c) {
    if (!hmap.count(c)) {
        hmap[c] = idCnt++;
        info.push_back({});
    }
    return hmap[c];
}

void init() {
    idCnt = 0;
    hmap.clear();
    info.clear();
    info.reserve(15);
    for (int i = 1; i <= 2; i++)
        for (int j = 1; j <= 2; j++)
            catcom[i][j].clear();
}

void insert_sorted(vector<Data>& vec, Data data) {
    auto it = lower_bound(vec.begin(), vec.end(), data);
    vec.insert(it, data);
}

int sell(int mID, int mCategory, int mCompany, int mPrice) {
    int id = getID(mID);
    info[id] = {mID, mCategory, mCompany, mPrice, false};
    insert_sorted(catcom[mCategory][mCompany], {id});
    return catcom[mCategory][mCompany].size();
}

int closeSale(int mID) {
    if (!hmap.count(mID)) return -1;
    int id = hmap[mID];
    hmap.erase(mID);
    info[id].removed = true;
    auto& vec = catcom[info[id].cat][info[id].com];
    vec.erase(find_if(vec.begin(), vec.end(), [&](const Data& d) { return d.id == id; }));
    return info[id].price;
}

int discount(int mCategory, int mCompany, int mAmount) {
    auto& vec = catcom[mCategory][mCompany];
    auto it = remove_if(vec.begin(), vec.end(), [&](Data& nx) {
        int id = nx.id;
        if (info[id].removed) return true;
        if (info[id].price <= mAmount) {
            hmap.erase(info[id].mid);
            info[id].removed = true;
            return true;
        }
        info[id].price -= mAmount;
        return false;
    });
    vec.erase(it, vec.end());
    sort(vec.begin(), vec.end());
    return vec.size();
}

RESULT show(int mHow, int mCode) {
    RESULT res = {0, {0}};
    vector<Data> tmp;
    tmp.reserve(15);
    for (int i = 1; i <= 2 && tmp.size() < 5; i++) {
        for (int j = 1; j <= 2 && tmp.size() < 5; j++) {
            if (mHow == 1 && i != mCode) continue;
            if (mHow == 2 && j != mCode) continue;
            for (const auto& nx : catcom[i][j]) {
                if (!info[nx.id].removed) {
                    tmp.push_back(nx);
                    if (tmp.size() == 5) break;
                }
            }
        }
    }
    if (tmp.size() > 1) sort(tmp.begin(), tmp.end());
    for (const auto& nx : tmp) res.IDs[res.cnt++] = info[nx.id].mid;
    return res;
}
```
