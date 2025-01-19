```cpp
#if 1
// 고유번호가 낮은 문명을 선택해야 하므로 mid를 그냥 맵에 기록해야 함.
#define _CRT_SECURE_NO_WARNIGS
#include<unordered_map>
#include<list>
#include<string.h>
using namespace std;
using pii = pair<int, int>;

unordered_map<int, int> hmap;   // hmap[id] = idx   : id인 부족이 사용하는 idx
int land[1003][1003];           // land[x][y] = idx : (x,y) 좌표를 점유하고 있는 부족의 idx
list<pii> tList[60003];         // tList[idx] = { (x,y), ... } : 부족별 점유중인 좌표 리스트
int tID[60003];                 // tID[idx] = id    : idx -> id
int idxCnt;                     // 현재까지 부여된 idx
int dx[4] = { 1,0,-1,0 }, dy[4] = { 0,1,0,-1 };

void init(int N)
{
    for (int i = 1; i <= idxCnt; i++) tList[i].clear();
    idxCnt = 0;
    hmap.clear();
    memset(land, 0, sizeof(land));
}

int newCivilization(int r, int c, int id)
{
    unordered_map<int, int> cnt;                        // (r,c) 주변의 {id, 개수}
    for (int i = 0; i < 4; i++) {
        int x = r + dx[i], y = c + dy[i];
        if (land[x][y]) cnt[tID[land[x][y]]]++;
    }
    int idx;
    if (cnt.empty()) {              // 주변에 다른 부족이 없는 경우
        idx = ++idxCnt;             // id->idx 부여, 등록
        tID[idx] = id;
        hmap[id] = idx;
    }
    else {                          // 주변에 다른 부족이 있는 경우
        pii ret;
        for (auto p : cnt) ret = max(ret, { p.second, -p.first });  // { 개수, -id } 최대값 구하기
        idx = hmap[-ret.second];    // 합병된 부족의 idx
    }

    land[r][c] = idx;
    tList[idx].push_back({ r,c });

    return tID[idx];
}

int removeCivilization(int id)
{
    if (!hmap.count(id)) return 0;
    int idx = hmap[id];
    hmap.erase(id);
    for (pii p : tList[idx]) land[p.first][p.second] = 0;

    return tList[idx].size();
}

int getCivilization(int r, int c)
{
    return tID[land[r][c]];
}

int getCivilizationArea(int id)
{
    return hmap.count(id) ? tList[hmap[id]].size() : 0;
}

int mergeCivilization(int id1, int id2)
{
    int idx1 = hmap[id1], idx2 = hmap[id2];

    hmap.erase(id2);

    /*
    * id2 -> id1 으로 합병
    * but, 땅의 수가 id1이 더 적으면 id1의 idx를 idx2로 사용
    */
    if (tList[idx1].size() < tList[idx2].size()) {
        tID[idx2] = id1;
        hmap[id1] = idx2;
        swap(idx1, idx2);
    }
    for (pii p : tList[idx2]) land[p.first][p.second] = idx1;
    tList[idx1].splice(tList[idx1].end(), tList[idx2]);

    return tList[idx1].size();
}
#endif // 0

```
