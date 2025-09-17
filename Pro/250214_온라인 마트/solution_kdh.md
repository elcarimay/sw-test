```cpp
#if 1 // 220 ms
#include <unordered_map>
#include <queue>
using namespace std;

struct RESULT { int cnt, IDs[5]; };

unordered_map<int, int> hmap;
struct Info {
    int mid, cat, com, price;
    bool removed;
}info[50003];

struct Data {
    int id, price;
    bool operator<(const Data& r)const {
        if (price != r.price) return price > r.price;
        return info[id].mid > info[r.id].mid;
    }
};

priority_queue<Data> catcom[6][6];
int idCnt, activeCnt[6][6], offset[6][6];

void cleanPQ(int c, int p) { // c:category, p:company
    while (!catcom[c][p].empty()) {
        Data cur = catcom[c][p].top(); 
        if (info[cur.id].removed || (cur.price != info[cur.id].price)) catcom[c][p].pop();
        else break;
    }
}

void init() {
    idCnt = 0, hmap.clear();
    for (int i = 1; i < 6; i++) for (int j = 1; j < 6; j++) {
        while(!catcom[i][j].empty()) catcom[i][j].pop();
        activeCnt[i][j] = offset[i][j] = 0;
    }
}

int sell(int mID, int mCategory, int mCompany, int mPrice) {
    int id = hmap[mID] = idCnt++;
    info[id] = { mID, mCategory, mCompany, mPrice + offset[mCategory][mCompany] };
    catcom[mCategory][mCompany].push({ id, info[id].price});
    return ++activeCnt[mCategory][mCompany];
}

int closeSale(int mID) {
    if (!hmap.count(mID)) return -1;
    int id = hmap[mID];
    hmap.erase(mID);
    activeCnt[info[id].cat][info[id].com]--;
    info[id].removed = true;
    return info[id].price - offset[info[id].cat][info[id].com];
}

int discount(int mCategory, int mCompany, int mAmount) {
    offset[mCategory][mCompany] += mAmount;
    while (!catcom[mCategory][mCompany].empty()) {
        cleanPQ(mCategory, mCompany);
        auto cur = catcom[mCategory][mCompany].top();
        auto& i = info[cur.id];
        if (i.price - offset[i.cat][i.com] <= 0) {
            catcom[mCategory][mCompany].pop();
            hmap.erase(i.mid);
            activeCnt[i.cat][i.com]--;
            i.removed = true;
        }
        else break;
    }
    return activeCnt[mCategory][mCompany];
}

struct Node {
    int id, price, cat, com;
    bool operator<(const Node& r)const {
        if (price != r.price) return price > r.price;
        return info[id].mid > info[r.id].mid;
    }
};
RESULT show(int mHow, int mCode) {
    RESULT res = { 0 };
    priority_queue<Node> mergePQ;
    for (int i = 1; i <= 5; i++) for (int j = 1; j <= 5; j++) {
        if ((mHow == 1 && i != mCode) || (mHow == 2 && j != mCode)) continue;
        cleanPQ(i, j);
        int cnt = 0;
        while (!catcom[i][j].empty() && cnt < 5) {
            auto cur = catcom[i][j].top(); catcom[i][j].pop();
            if (info[cur.id].removed) continue;
            mergePQ.push({ cur.id, cur.price - offset[i][j], i , j });
            cnt++;
        }
    }

    priority_queue<Node> tmp;
    while (!mergePQ.empty()) {
        tmp.push(mergePQ.top());
        if(res.cnt < 5) res.IDs[res.cnt++] = info[mergePQ.top().id].mid;
        mergePQ.pop();
    }
    Node n = {};
    while (!tmp.empty()) {
        n = tmp.top(); tmp.pop();
        catcom[n.cat][n.com].push({ n.id, n.price + offset[n.cat][n.com] });
    }
    return res;
}
#endif // 1
```
