```cpp
#if 1 // 220 ms
#include <unordered_map>
#include <queue>
using namespace std;

struct RESULT { int cnt, IDs[5]; };

unordered_map<int, int> hmap;
struct Info {
    int mid, cc, price;
}info[50003];

priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> catcom[6][6];
int idCnt, activeCnt[6][6], offset[6][6];

void cleanPQ(int c, int p) { // c:category, p:company
    while (!catcom[c][p].empty()) {
        auto cur = catcom[c][p].top(); 
        if (!hmap.count(info[cur.second].mid) || (cur.first != info[cur.second].price)) catcom[c][p].pop();
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
    info[id] = { mID, mCategory*10+mCompany, mPrice + offset[mCategory][mCompany] };
    catcom[mCategory][mCompany].push({ info[id].price, id});
    return ++activeCnt[mCategory][mCompany];
}

int closeSale(int mID) {
    if (!hmap.count(mID)) return -1;
    int id = hmap[mID];
    hmap.erase(mID);
    int c = info[id].cc / 10, p = info[id].cc % 10;
    activeCnt[c][p]--;
    return info[id].price - offset[c][p];
}

int discount(int mCategory, int mCompany, int mAmount) {
    offset[mCategory][mCompany] += mAmount;
    while (!catcom[mCategory][mCompany].empty()) {
        cleanPQ(mCategory, mCompany);
        auto cur = catcom[mCategory][mCompany].top();
        auto& i = info[cur.second];
        int c = i.cc / 10, p = i.cc % 10;
        if (i.price - offset[c][p] <= 0) {
            catcom[mCategory][mCompany].pop();
            hmap.erase(i.mid);
            activeCnt[c][p]--;
        }
        else break;
    }
    return activeCnt[mCategory][mCompany];
}

struct Node {
    int id, price, cc;
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
            if (!hmap.count(info[cur.second].mid)) continue;
            mergePQ.push({ cur.second, cur.first - offset[i][j], i*10+j });
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
        int c = n.cc / 10, p = n.cc % 10;
        catcom[c][p].push({ n.price + offset[c][p], n.id });
    }
    return res;
}
#endif // 1
```
