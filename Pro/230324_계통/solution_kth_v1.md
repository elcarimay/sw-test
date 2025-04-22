```cpp
/*
* getDistance : 1단계씩 올라가는 LCA
* getCount : 거리 5까지의 자식노드 개수 기록, 부모노드를 타고 올라가며 활용
*/
#define MAXL    (11)    

#include<vector>
#include<unordered_map>
#include<string>
#include<string.h>
using namespace std;

const int LM = 50003;
int idcnt;
unordered_map<string, int> idmap;       // idmap[name] = 1~
int D[LM];                              // depth
int P[LM];                              // parent
vector<int> C[LM];                      // child list
int childCnt[LM][5];                    // childCnt[x][d] : x노드의 d깊이 자식노드 개수

void init(char mRootSpecies[MAXL])
{
    for (int i = 0; i < idcnt; i++) C[i].clear();
    memset(childCnt, 0, sizeof(childCnt));
    childCnt[1][0] = 1;
    idmap.clear();
    idmap[mRootSpecies] = idcnt = 1;
}

void add(char mSpecies[MAXL], char mParentSpecies[MAXL])
{
    int pid = idmap[mParentSpecies];
    int cid = idmap[mSpecies] = ++idcnt;
    P[cid] = pid;
    C[pid].push_back(cid);
    D[cid] = D[pid] + 1;

    for (int i = 0; i <= 4 && cid; i++, cid = P[cid])
        childCnt[cid][i]++;
}

int getDistance(char mSpecies1[MAXL], char mSpecies2[MAXL])
{
    int a = idmap[mSpecies1], b = idmap[mSpecies2];
    int dist = 0;

    while (D[a] < D[b]) dist++, b = P[b];
    while (D[a] > D[b]) dist++, a = P[a];
    while (a != b) dist += 2, a = P[a], b = P[b];

    return dist;
}

int getCount(char mSpecies[MAXL], int dist)
{
    int x = idmap[mSpecies];
    int cnt = childCnt[x][dist];
    while (dist-- && x > 1) {
        if (dist == 0) cnt++;
        else cnt += childCnt[P[x]][dist] - childCnt[x][dist - 1];
        x = P[x];
    }
    return cnt;
}
```
