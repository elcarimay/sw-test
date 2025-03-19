```cpp
#if 1
// union-find ver.
// 사각형입력시 한칸을 벗어나도 다음꺼와 union할때는 옆에 붙어있어야만 하므로 좌상단 기준으로만 입력하면 됨
// 초기에 부모를 자기자신으로 하고 rank는 0으로 입력
// idmap에서의 id를 초기에 group id로 입력함
// grouping할때 list와 vector를 사용해봤는데 속도차이없음
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <list>
using namespace std;

#define MAXN 15003

struct Rect {
    int sr, sc, er, ec;
}rect[MAXN];
int p[MAXN], rnk[MAXN];

int find(int x) {
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

vector<int> g[53][53];
list<int> group[MAXN];
//vector<int> group[MAXN];
int totalCnt; // total group count
void unionSet(int x, int y) {
    int rootX = find(x), rootY = find(y);
    if (rootX == rootY) return;
    if (rnk[rootX] < rnk[rootY]) swap(rootX, rootY);
    p[rootY] = rootX; // rooY -> rootX
    if (rnk[rootX] == rnk[rootY]) rnk[rootX]++;
    group[rootX].splice(group[rootX].end(), group[rootY]);
    //group[rootX].insert(group[rootX].end(), group[rootY].begin(), group[rootY].end());
    totalCnt--;
}

int L, N, GN;
unordered_map<int, int> idMap;
int idCnt;
void init(int L, int N) {
    ::L = L, ::N = N, GN = N / L, idCnt = totalCnt = 0, idMap.clear();
    for (auto& nx : group) nx.clear();
    memset(g, 0, sizeof(g));
}

int getID(int c) {
    return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}

bool overlap(int o, int n) {
    int or1 = rect[o].sr, or2 = rect[o].er;
    int oc1 = rect[o].sc, oc2 = rect[o].ec;
    int nr1 = rect[n].sr, nr2 = rect[n].er;
    int nc1 = rect[n].sc, nc2 = rect[n].ec;
    if (!(nr2 < or1 || or2 < nr1 || nc2 < oc1 || oc2 < nc1))
        return true;
    return false;
}

int draw(int mID, int mRow, int mCol, int mHeight, int mWidth) {
    int id = getID(mID);
    rect[id] = { mRow, mCol, mRow + mHeight - 1, mCol + mWidth - 1 };
    group[id].insert(group[id].end(), id); // id가 그룹아이디가 됨
    p[id] = id, rnk[id] = 0; // root는 0으로 만듬
    totalCnt++;
    int sr = max(mRow / L - 1, 0), er = min(mRow / L + 1, GN);
    int sc = max(mCol / L - 1, 0), ec = min(mCol / L + 1, GN);
    for (int r = sr; r <= er; r++) for (int c = sc; c <= ec; c++)
        for (auto nx : g[r][c]) if (overlap(id, nx)) unionSet(id, nx);
    g[mRow / L][mCol / L].push_back(id);
    return (int)group[find(id)].size();
}

int getRectCount(int mID) {
    if (!idMap.count(mID)) return 0;
    return (int)group[find(getID(mID))].size();
}

int countGroup() {
    return totalCnt;
}
#endif // 1

```
