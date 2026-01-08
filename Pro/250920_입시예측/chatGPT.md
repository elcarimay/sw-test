```cpp
#include <vector>
#include <algorithm>
#include <cstring>
using namespace std;

#define MAXS 20003
#define MAXU 33

struct Student {
    int sum[MAXU];
    int univ;
    bool alive;
} st[MAXS];

struct Data {
    int id, sum;
};

struct Univ {
    int* weight;
} uv[MAXU];

int N, M;

// 대학별 학생 리스트
vector<Data> uni[MAXU];

// lazy delete
bool dead[MAXS];

// 선택 여부 (timestamp)
int taken[MAXS];
int token = 1;

// dirty flag (선택적 최적화)
bool dirty[MAXU];


// ---------------- init ----------------
void init(int N, int M, int mWeight[][5]) {
    ::N = N;
    ::M = M;

    for (int i = 0; i < M; i++) {
        uni[i].clear();
        uv[i].weight = mWeight[i];
        dirty[i] = false;
    }

    memset(dead, 0, sizeof(dead));
}


// ---------------- add ----------------
void add(int mID, int mScores[5]) {
    st[mID].alive = true;
    st[mID].univ = -1;

    for (int i = 0; i < M; i++) {
        int sum = 0;
        for (int j = 0; j < 5; j++)
            sum += mScores[j] * uv[i].weight[j];

        st[mID].sum[i] = sum;
        uni[i].push_back({mID, sum});
        dirty[i] = true;
    }
}


// ---------------- erase (lazy) ----------------
void erase(int mID) {
    dead[mID] = true;
    st[mID].alive = false;
}


// ---------------- suggest ----------------
int suggest(int mID) {
    token++;

    for (int u = 0; u < M; u++) {
        auto &v = uni[u];
        if (v.empty()) continue;

        int K = min(N, (int)v.size());

        // 필요할 때만 top-K 재계산
        if (dirty[u]) {
            nth_element(v.begin(), v.begin() + K, v.end(),
                [](const Data& a, const Data& b) {
                    if (a.sum != b.sum) return a.sum > b.sum;
                    return a.id < b.id;
                });

            sort(v.begin(), v.begin() + K,
                [](const Data& a, const Data& b) {
                    if (a.sum != b.sum) return a.sum > b.sum;
                    return a.id < b.id;
                });

            dirty[u] = false;
        }

        int cnt = 0;
        for (int i = 0; i < K; i++) {
            int id = v[i].id;

            if (dead[id]) continue;
            if (taken[id] == token) continue;

            taken[id] = token;
            cnt++;

            if (id == mID) return u + 1;
            if (cnt == N) break;
        }
    }
    return -1;
}

```
