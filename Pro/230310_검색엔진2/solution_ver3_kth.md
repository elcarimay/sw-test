```cpp
#if 1 // 130 ms
/*
* set으로 우선순위 관리하지만 복잡한거에 비해 효율이 그렇게 좋지는 않다..
*/
#define _CRT_SECURE_NO_WARNINGS
#include<string>
#include<unordered_map>
#include<vector>
#include<set>
#include<algorithm>
#include<string.h>
#include<stdio.h>
using namespace std;

struct Result { int mOrder, mRank; };

char S[8003][10];
int group[8003];
int idcnt, gViews[8003];
vector<int> gList[8003];

struct Comp {
    bool operator()(const int l, const int r) const {
        int cl = gViews[group[l]];
        int cr = gViews[group[r]];
        if (cl != cr) return cl > cr;
        return strcmp(S[l], S[r]) < 0;
    }
};

unordered_map<string, int> idmap;
unordered_map<string, set<int, Comp>> prefix;

void init() {
    for (int i = 1; i <= idcnt; i++) gList[i].clear();
    idmap.clear();
    idcnt = 0;
    prefix.clear();
}

void updateSet(int gid, bool INSERT) {
    for (int x : gList[gid]) {
        string s;
        for (int i = -1; i == -1 || S[x][i]; i++) {
            if (i >= 0) s += S[x][i];

            if (INSERT) prefix[s].insert(x);
            else prefix[s].erase(x);
        }
    }
}

void update(int gid, int c) {
    updateSet(gid, 0);
    gViews[gid] += c;
    updateSet(gid, 1);
}

void search(char mStr[], int mCount) {
    int sid = idmap[mStr];
    if (sid) update(group[sid], mCount);

    else {
        int sid = idmap[mStr] = ++idcnt;
        group[sid] = sid;
        gList[sid].push_back(sid);
        strcpy(S[sid], mStr);
        gViews[sid] = mCount;

        string s;
        prefix[s].insert(sid);
        for (int i = 0; mStr[i]; i++) {
            s += mStr[i];
            prefix[s].insert(sid);
        }
    }
}

Result recommend(char mStr[]) {
    string s;
    for (int i = -1; i == -1 || mStr[i]; i++) {
        if (i >= 0) s += mStr[i];
        auto& pre = prefix[s];
        auto it = pre.begin();
        for (int j = 0; j < 5 && it != pre.end(); j++, ++it) {
            if (strcmp(S[*it], mStr) == 0) {
                update(group[*it], 1);
                return { i + 1, j + 1 };
            }
        }
    }
}

int relate(char mStr1[], char mStr2[]) {
    int gid1 = group[idmap[mStr1]], gid2 = group[idmap[mStr2]];
    int v1 = gViews[gid1], v2 = gViews[gid2];
    update(gid1, v2);
    update(gid2, v1);
    for (int x : gList[gid2]) {
        group[x] = gid1;
        gList[gid1].push_back(x);
    }
    return gViews[gid1];
}

void rank(char mPrefix[], int mRank, char mReturnStr[]) {
    auto& pre = prefix[mPrefix];
    strcpy(mReturnStr, S[*next(pre.begin(), mRank - 1)]);
}
#endif
```
