```cpp
#if 1
#include <unordered_map>
#include <vector>
#include <set>
#include <math.h>
#include <algorithm>
using namespace std;

struct Data {
    int id, rank;
    bool operator<(const Data& r)const {
        if (rank != r.rank) return rank < r.rank;
        return id > r.id;
    }
};

struct Goods {
    int id, cat, score, rank;
    set<Data>::iterator it;
}goods[50003];

set<Data> cat[6];
unordered_map<int, int> idmap; // mGoodsID, int
int idCnt;

int cal_rank(int rank) {
    //return rank == 100 ? 1 : ceil((double)(100 - rank) / 20);
    return rank == 100 ? 1 : (99 - rank) / 20 + 1;
}

void init() {
    idmap.clear(); idCnt = 0;
    for (int i = 1; i <= 5; i++) cat[i].clear();
}

void add(int mGoodsID, int mCategory, int mScore) {
    int id = idmap[mGoodsID] = idCnt++;
    int rank = cal_rank(mScore);
    goods[id] = { mGoodsID, mCategory, mScore, rank };
    goods[id].it = cat[mCategory].insert({ mGoodsID, rank }).first;
}

void remove(int mGoodsID) {
    auto& g = goods[idmap[mGoodsID]];
    cat[g.cat].erase(g.it);
}

void purchase(int mGoodsID) {
    auto& g = goods[idmap[mGoodsID]];
    cat[g.cat].erase(g.it);
    g.score = min(g.score + 5, 100);
    g.rank = cal_rank(g.score);
    g.it = cat[g.cat].insert({ g.id, g.rank }).first;
}

void takeBack(int mGoodsID) {
    auto& g = goods[idmap[mGoodsID]];
    cat[g.cat].erase(g.it);
    g.score = max(g.score - 10, 0);
    g.rank = cal_rank(g.score);
    g.it = cat[g.cat].insert({ g.id, g.rank }).first;
}

int que[50003], head, tail;
void changeScore(int mCategory, int mChangeScore) {
    head = tail = 0;
    for (auto it = cat[mCategory].begin(); it != cat[mCategory].end(); it++) que[tail++] = it->id;
    while(head < tail){
        auto& g = goods[idmap[que[head++]]];
        cat[g.cat].erase(g.it);
        g.score = max(g.score + mChangeScore, 0);
        g.score = min(g.score, 100);
        g.rank = cal_rank(g.score);
        g.it = cat[g.cat].insert({ g.id, g.rank }).first;
    }
}

int getTopRank(int mCategory) {
    if (mCategory == 0) {
        vector<Data> tmp;
        for (int i = 1; i <= 5; i++) {
            if (cat[i].empty()) continue;
            tmp.push_back({ cat[i].begin()->id, cat[i].begin()->rank });
        }
        partial_sort(tmp.begin(), tmp.begin() + 1, tmp.end());
        return tmp[0].id;
    }
    return cat[mCategory].begin()->id;
}
#endif // 0

```
