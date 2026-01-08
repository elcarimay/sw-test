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
    
    // 비교 연산자 인라인으로 최적화
    inline bool operator>(const Data& other) const {
        return sum != other.sum ? sum > other.sum : id < other.id;
    }
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

// dirty flag
bool dirty[MAXU];

// 삭제된 학생 수 추적 (garbage collection용)
int deadCount[MAXU];

// 임계값 (전체의 30% 이상이 dead면 정리)
const int GC_THRESHOLD_PERCENT = 30;


// ---------------- init ----------------
void init(int N, int M, int mWeight[][5]) {
    ::N = N;
    ::M = M;

    for (int i = 0; i < M; i++) {
        uni[i].clear();
        uni[i].reserve(MAXS); // 메모리 사전 할당
        uv[i].weight = mWeight[i];
        dirty[i] = false;
        deadCount[i] = 0;
    }

    memset(dead, 0, sizeof(dead));
    memset(taken, 0, sizeof(taken));
    token = 1;
}


// ---------------- add ----------------
void add(int mID, int mScores[5]) {
    st[mID].alive = true;
    st[mID].univ = -1;

    for (int i = 0; i < M; i++) {
        int sum = 0;
        // 루프 언롤링으로 최적화
        sum += mScores[0] * uv[i].weight[0];
        sum += mScores[1] * uv[i].weight[1];
        sum += mScores[2] * uv[i].weight[2];
        sum += mScores[3] * uv[i].weight[3];
        sum += mScores[4] * uv[i].weight[4];

        st[mID].sum[i] = sum;
        uni[i].push_back({mID, sum});
        dirty[i] = true;
    }
}


// ---------------- erase (lazy) ----------------
void erase(int mID) {
    if (dead[mID]) return; // 중복 삭제 방지
    
    dead[mID] = true;
    st[mID].alive = false;
    
    // 삭제 카운트 증가
    for (int i = 0; i < M; i++) {
        deadCount[i]++;
    }
}


// ---------------- garbage collection ----------------
inline void garbageCollect(int u) {
    auto& v = uni[u];
    int size = v.size();
    
    // 삭제된 학생이 30% 이상일 때만 정리
    if (deadCount[u] * 100 < size * GC_THRESHOLD_PERCENT) return;
    
    // dead 학생 제거
    auto it = remove_if(v.begin(), v.end(), 
        [](const Data& d) { return dead[d.id]; });
    v.erase(it, v.end());
    
    deadCount[u] = 0;
    dirty[u] = true;
}


// ---------------- suggest ----------------
int suggest(int mID) {
    token++;

    for (int u = 0; u < M; u++) {
        auto& v = uni[u];
        if (v.empty()) continue;

        // Garbage collection
        garbageCollect(u);
        
        int size = v.size();
        if (size == 0) continue;
        
        int K = min(N, size);

        // 필요할 때만 top-K 재계산
        if (dirty[u]) {
            // partial_sort 사용: nth_element + sort보다 빠름
            partial_sort(v.begin(), v.begin() + K, v.end(),
                [](const Data& a, const Data& b) {
                    return a > b;
                });
            dirty[u] = false;
        }

        int cnt = 0;
        for (int i = 0; i < K && i < size; i++) {
            int id = v[i].id;

            // 빠른 경로: 이미 체크된 경우
            if (taken[id] == token) continue;
            if (dead[id]) continue;

            taken[id] = token;
            cnt++;

            // Early exit: mID를 찾으면 즉시 반환
            if (id == mID) return u + 1;
            
            if (cnt >= N) break;
        }
    }
    return -1;
}

```
