```cpp
#if 1 // 65 ms
/*
* 문자열에 id를 부여하고 char 배열에 기록한다.
* prefix에는 id를 기록
* group번호도 hash가 아니라 배열에 기록
* compare에서 hash 접근이 사라지고 문자열 비교는 char로 할 수 있어 성능 개선
*/
#define _CRT_SECURE_NO_WARNINGS
#include<string>
#include<unordered_map>
#include<vector>
#include<algorithm>
#include<string.h>
#include<stdio.h>
using namespace std;

struct Result { int mOrder, mRank; };

int idcnt, gViews[8003], group[8003];
char S[8003][10];
unordered_map<string, int> idmap;
unordered_map<string, vector<int>> prefix;

void init() {
    idmap.clear();
    idcnt = 0;
    prefix.clear();
}

void search(char mStr[], int mCount) {
    int sid = idmap[mStr];
    if (sid) gViews[group[sid]] += mCount;

    else {
        int sid = idmap[mStr] = ++idcnt;
        group[sid] = sid;
        strcpy(S[sid], mStr);
        gViews[sid] = mCount;
        
        string s;
        prefix[s].push_back(sid);
        for (int i = 0; mStr[i]; i++) {
            s += mStr[i];
            prefix[s].push_back(sid);
        }
    }
}

bool comp(int l, int r) {
    int cl = gViews[group[l]];
    int cr = gViews[group[r]];
    if (cl != cr) return cl > cr;
    return strcmp(S[l], S[r]) < 0;
}

Result recommend(char mStr[]) {
    string s;
    for (int i = -1; i == -1 || mStr[i]; i++) {
        if (i >= 0) s += mStr[i];
        auto& pre = prefix[s];
        int c = min(5, (int)pre.size());
        partial_sort(pre.begin(), pre.begin() + c, pre.end(), comp);
        for (int idx = 0; idx < c; idx++)
            if (strcmp(S[pre[idx]], mStr) == 0) {
                gViews[group[pre[idx]]]++;
                return { i + 1, idx + 1 };
            }
    }
}

int relate(char mStr1[], char mStr2[]) {
    int gid1 = group[idmap[mStr1]], gid2 = group[idmap[mStr2]];

    gViews[gid1] += gViews[gid2];
    for (int i = 1; i <= idcnt; i++)
        if (group[i] == gid2) group[i] = gid1;
    return gViews[gid1];
}

void rank(char mPrefix[], int mRank, char mReturnStr[]) {
    auto& pre = prefix[mPrefix];
    partial_sort(pre.begin(), pre.begin() + mRank, pre.end(), comp);
    strcpy(mReturnStr, S[pre[mRank - 1]]);
}
#endif
```
