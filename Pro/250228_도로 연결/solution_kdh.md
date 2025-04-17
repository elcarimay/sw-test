```cpp
#if 1
// 250124_사각형 그리기 유사문제
// 차량 이동여부만 해결하면 되는데 출발/도착위치가 overlap되는지만 확인해주면 됨
#include <vector>
#include <algorithm>
#include <list>
using namespace std;
using pib = pair<int, bool>;
#define MAXN 15003

struct Road {
    int sr, sc, er, ec, dir;
}road[MAXN];
int p[MAXN], rnk[MAXN];

int find(int x) {
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

vector<int> g[503][503];
list<int> group[MAXN];
int totalCnt; // total group count
void unionSet(int x, int y) {
    int rootX = find(x), rootY = find(y);
    if (rootX == rootY) return;
    if (rnk[rootX] < rnk[rootY]) swap(rootX, rootY);
    p[rootY] = rootX; // rooY -> rootX
    if (rnk[rootX] == rnk[rootY]) rnk[rootX]++;
    group[rootX].splice(group[rootX].end(), group[rootY]);
    totalCnt--;
}

int L, N, GN, idCnt;
void init(int L, int N){
    ::L = L, ::N = N, GN = N / L, idCnt = totalCnt = 0;
    for (auto& nx : group) nx.clear();
    for (int i = 0; i < 503; i++) for (int j = 0; j < 503; j++) g[i][j].clear();
}

bool overlap(int o, int n) {
    int or1 = road[o].sr, or2 = road[o].er, oc1 = road[o].sc, oc2 = road[o].ec;
    int nr1 = road[n].sr, nr2 = road[n].er, nc1 = road[n].sc, nc2 = road[n].ec;
    return !(nr2 < or1 || or2 < nr1 || nc2 < oc1 || oc2 < nc1) ? true : false;
}

int build(int mDir, int mRow, int mCol, int mLength){
    if (!mDir) road[idCnt] = { mRow, mCol, mRow, mCol + mLength - 1, mDir };
    else road[idCnt] = { mRow, mCol, mRow + mLength - 1, mCol, mDir };
    group[idCnt].insert(group[idCnt].end(), idCnt);
    p[idCnt] = idCnt, rnk[idCnt] = 0, totalCnt++;
    int sr = max(mRow / L, 0), er = min(mRow / L + 1, GN);
    int sc = max(mCol / L, 0), ec = min(mCol / L + 1, GN);
    for (int r = sr; r <= er; r++) for (int c = sc; c <= ec; c++)
        for (int nx : g[r][c]) if (overlap(idCnt, nx)) unionSet(idCnt, nx);
    if (!mDir && (mCol / L != (mCol + mLength) / L)) g[mRow / L][(mCol + mLength) / L].push_back(idCnt);
    else if ((mRow / L != (mRow + mLength) / L)) g[(mRow + mLength) / L][mCol / L].push_back(idCnt);
    g[mRow / L][mCol / L].push_back(idCnt++);
	return totalCnt;
}

pib overlapFlag(int R, int C) {
    bool flag = false;
    for (int id : g[R / L][C / L]) {
        auto& r = road[id];
        if (!r.dir && R == r.sr && r.sc <= C && C <= r.ec) flag = true;
        else if (C == r.sc && r.sr <= R && R <= r.er) flag = true;
        if (flag) {
            return { find(id), true };
        }
    }
    return { -1, false };
}

int checkRoute(int mSRow, int mSCol, int mERow, int mECol){
    bool overlapFlag1 = false, overlapFlag2 = false;
    pib start = overlapFlag(mSRow, mSCol); // group id, overlap flag
    pib end = overlapFlag(mERow, mECol);
    if (start.second == false || end.second == false) return -1;
    return start.first == end.first ? 1 : 0;
}
#endif // 0

```
