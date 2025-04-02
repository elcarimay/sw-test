```cpp
#if 1
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;

#define MAXP 100
#define MAXN 20000
#define MAXM 1000
#define MAXW 50003
#define ADDED 1
#define REMOVED 2

unordered_map<int, int> idMap;
int idCnt;

struct Data {
    int r, c, len, state; // 1: ADDED, 2: REMOVED
};
struct Data2 {
    int c, len;
};
vector<Data2> blank[MAXN + 3];
int rowMax[MAXN + 3], pageMax[MAXN / MAXP + 3];
Data word[MAXW + 3];

int N, M;
void init(int N, int M) {
    ::N = N, ::M = M, idCnt = 0, idMap.clear();
    for (int i = 0; i < N; i++)
        blank[i].clear(), blank[i].push_back({ 0,M });
    for (int i = 0; i < N; i++) rowMax[i] = M;
    for (int i = 0; i <= (N - 1) / MAXP; i++) pageMax[i] = M;
    for (int i = 0; i < MAXW; i++) word[i] = {};
}

int writeWord(int mId, int mLen) {
    int page = -1;
    for (int i = 0; i <= (N - 1) / MAXP; i++)
        if (pageMax[i] >= mLen) {
            page = i; break;
        }
    if (page == -1) return -1;
    int id, r = 0, c = 0;
    if (!idMap.count(mId)) id = idMap[mId] = idCnt++;
    else id = idMap[mId];
    for (int i = page * MAXP; i < (page + 1) * MAXP; i++) {
        if (rowMax[i] >= mLen) {
            r = i; break;
        }
    }
    for (int i = 0; i < blank[r].size(); i++)
        if (blank[r][i].len >= mLen) { //빈 공간이 있을때
            word[id] = { r,blank[r][i].c, mLen, ADDED };
            blank[r][i].c += mLen, blank[r][i].len -= mLen;
            if (blank[r][i].len == 0) blank[r].erase(blank[r].begin() + i);
            break;
        }
    rowMax[r] = 0;
    for (int i = 0; i < blank[r].size(); i++)
        rowMax[r] = max(rowMax[r], blank[r][i].len);
    page = r / MAXP; int maxv = 0;
    for (int i = page * MAXP; i < (page + 1) * MAXP; i++)
        maxv = max(maxv, rowMax[i]);
    pageMax[page] = maxv;
    return r;
}

bool cmp(Data2& a, Data2& b) {
    return a.c < b.c;
}

int eraseWord(int mId) {
    if (!idMap.count(mId)) return -1;
    int id = idMap[mId];
    if (word[id].state == REMOVED) return -1;
    word[id].state = REMOVED;
    int r = word[id].r, c = word[id].c, len = word[id].len;
    bool left = false, right = false;
    int lr, rr;
    for (int i = 0; i < blank[r].size(); i++) {
        if (blank[r][i].c + blank[r][i].len == c)
            left = true, lr = i;
        else if (c + len == blank[r][i].c)
            right = true, rr = i;
        if (c + len < blank[r][i].c) break;
    }
    // 좌우측 빈칸처리
    if (left == true && right == true) {
        blank[r][lr].len += len + blank[r][rr].len;
        blank[r].erase(blank[r].begin() + rr);
    }
    else if (left == true && right == false)
        blank[r][lr].len += len;
    else if (left == false && right == true) {
        blank[r][rr].c = c;
        blank[r][rr].len += len;
    }
    else { // left = false, right = false
        blank[r].push_back({ c,len });
        sort(blank[r].begin(), blank[r].end(), cmp);
    }
    // max/min update
    for (int i = 0; i < blank[r].size(); i++)
        rowMax[r] = max(rowMax[r], blank[r][i].len);
    pageMax[r / MAXP] = max(pageMax[r / MAXP], rowMax[r]);
    return r;
}
#endif // 1

```
