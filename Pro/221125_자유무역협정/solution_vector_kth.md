```cpp
#if 1 // 61 ms
// 개별로 group을 설정하고 그룹간 project는 따로 pcnt[a][b]로 관리함
// 프로젝트를 하고 있는지 여부를 pcnt으로 알아보고 반환
#include<unordered_map>
#include<unordered_set>
#include<string>
#include<vector>
#include<algorithm>
#include<string.h>
using namespace std;

int N;                                  // N = numA + numB
unordered_map<string, int> idmap;       // idmap[name] = cid(0 ~ N-1)
vector<int> allyList[1003];             // allyList[gid] = { cid, .. }
int group[1003];                        // group[cid] = gid
int pcnt[1003][1003];                   // pcnt[gid1][gid2] = gid1그룹과 gid2그룹의 협업 중인 project 수

void init(int numA, char listA[500][11], int numB, char listB[500][11]) {
    idmap.clear();
    memset(pcnt, 0, sizeof(pcnt));
    N = numA + numB;
    for (int i = 0; i < N; i++) allyList[i] = { i };
    for (int i = 0; i < numA; i++) idmap[listA[i]] = group[i] = i;
    for (int i = 0; i < numB; i++) idmap[listB[i]] = group[numA + i] = numA + i;
}

void startProject(char mCompanyA[11], char mCompanyB[11]) {
    int a = group[idmap[mCompanyA]], b = group[idmap[mCompanyB]];   // a,b:그룹번호
    pcnt[a][b]++;
    pcnt[b][a]++;
}

void finishProject(char mCompanyA[11], char mCompanyB[11]) {
    int a = group[idmap[mCompanyA]], b = group[idmap[mCompanyB]];   // a,b:그룹번호
    pcnt[a][b]--;
    pcnt[b][a]--;
}

void ally(char mCompany1[111], char mCompany2[11]) {
    int a = group[idmap[mCompany1]], b = group[idmap[mCompany2]];   // a,b:그룹번호
    if (a == b) return;
    if (allyList[a].size() < allyList[b].size()) swap(a, b);

    for (int x : allyList[b]) {     // b->a 그룹 이동
        group[x] = a;
        allyList[a].push_back(x);
    }
    for (int i = 0; i < N; i++) {
        pcnt[a][i] += pcnt[b][i];   // a의 i와의 프로젝트 수 += b의 프로젝트 수
        pcnt[i][a] += pcnt[i][b];   // i의 a와의 프로젝트 수 += b와의 프로젝트 수
        pcnt[i][b] = 0;             // b와의 프로젝트 수 0으로 변경
    }
}

int conflict(char mCompany1[11], char mCompany2[11]) {
    int a = group[idmap[mCompany1]], b = group[idmap[mCompany2]];   // a,b:그룹번호
    int ret = 0;
    for (int i = 0; i < N; i++)
        if (pcnt[a][i] && pcnt[b][i]) ret += allyList[i].size();
    return ret;
}
#endif // 1 // 61 ms
```
