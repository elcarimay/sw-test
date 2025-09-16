```cpp
#include <unordered_map>
#include <vector>
#include <algorithm>
using namespace std;

#define MAXN 50003

struct RESULT { int cnt, IDs[5]; };

unordered_map<int, int> hmap;
struct Info {
    int mid, cat, com, price;
    bool removed;
} info[MAXN];

int idCnt;
struct Data {
    int id;
    bool operator<(const Data& r) const {
        if (info[id].price != info[r.id].price) return info[id].price < info[r.id].price;
        return info[id].mid < info[r.id].mid;
    }
};
vector<Data> catcom[6][6];

int getID(int c) {
    return hmap.count(c) ? hmap[c] : hmap[c] = idCnt++;
}

void init() {
    idCnt = 0;
    hmap.clear();
    for (int i = 1; i < 6; i++)
        for (int j = 1; j < 6; j++)
            catcom[i][j].clear();
}

void insert_sorted(vector<Data>& vec, Data data) {
    auto it = lower_bound(vec.begin(), vec.end(), data);
    vec.insert(it, data);
}

int sell(int mID, int mCategory, int mCompany, int mPrice) {
    int id = getID(mID);
    info[id] = { mID, mCategory, mCompany, mPrice, false };
    insert_sorted(catcom[mCategory][mCompany], { id });
    return (int)catcom[mCategory][mCompany].size();
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
    vector<Data>& vec = catcom[mCategory][mCompany];
    vector<Data> new_vec;
    new_vec.reserve(vec.size());
    for (auto& nx : vec) {
        int id = nx.id;
        if (info[id].removed) continue;
        if (info[id].price - mAmount <= 0) {
            hmap.erase(info[id].mid);
            info[id].removed = true;
        } else {
            info[id].price -= mAmount;
            new_vec.push_back(nx);
        }
    }
    vec.swap(new_vec);
    sort(vec.begin(), vec.end()); // 정렬 유지
    return (int)vec.size();
}

RESULT show(int mHow, int mCode) {
    RESULT res = { 0, {0} };
    vector<Data> tmp;
    tmp.reserve(5);
    for (int i = 1; i <= 5 && tmp.size() < 5; i++) {
        for (int j = 1; j <= 5 && tmp.size() < 5; j++) {
            if (mHow == 1 && i != mCode) continue;
            if (mHow == 2 && j != mCode) continue;
            for (auto& nx : catcom[i][j]) {
                if (!info[nx.id].removed) {
                    tmp.push_back(nx);
                    if (tmp.size() == 5) break;
                }
            }
        }
    }
    if (tmp.size() > 1) sort(tmp.begin(), tmp.end());
    for (auto& nx : tmp) res.IDs[res.cnt++] = info[nx.id].mid;
    return res;
}
```
