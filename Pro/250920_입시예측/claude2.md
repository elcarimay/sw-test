```cpp
#include <vector>
#include <algorithm>
#include <cstring>
using namespace std;

#define MAXS 20003
#define MAXU 33

struct Data {
    int id, sum;
    
    inline bool operator>(const Data& other) const {
        return sum != other.sum ? sum > other.sum : id < other.id;
    }
};

struct Univ {
    vector<Data> all;        // 전체 학생 리스트
    int topK[MAXS];          // Top-K 학생 ID 캐시
    short topKSize;          // Top-K 크기
    short deadCnt;           // 삭제된 학생 수
    bool dirty;              // 재계산 필요 여부
    int* weight;             // 가중치 포인터
} unis[MAXU];

int N, M;

// lazy delete
bool dead[MAXS];

// 선택 여부 (timestamp)
int taken[MAXS];
int token = 1;

// Garbage Collection 임계값
const int GC_THRESHOLD = 30;  // 30% 이상 dead면 정리


// ---------------- init ----------------
void init(int N, int M, int mWeight[][5]) {
    ::N = N;
    ::M = M;
    
    for (int i = 0; i < M; i++) {
        unis[i].all.clear();
        unis[i].all.reserve(MAXS);
        unis[i].topKSize = 0;
        unis[i].deadCnt = 0;
        unis[i].dirty = false;
        unis[i].weight = mWeight[i];
    }
    
    memset(dead, 0, sizeof(dead));
    memset(taken, 0, sizeof(taken));
    token = 1;
}


// ---------------- add ----------------
void add(int mID, int mScores[5]) {
    for (int i = 0; i < M; i++) {
        // 루프 언롤링으로 점수 계산 최적화
        int sum = mScores[0] * unis[i].weight[0]
                + mScores[1] * unis[i].weight[1]
                + mScores[2] * unis[i].weight[2]
                + mScores[3] * unis[i].weight[3]
                + mScores[4] * unis[i].weight[4];
        
        unis[i].all.push_back({mID, sum});
        unis[i].dirty = true;
    }
}


// ---------------- erase (lazy) ----------------
void erase(int mID) {
    if (dead[mID]) return;  // 중복 삭제 방지
    
    dead[mID] = true;
    
    for (int i = 0; i < M; i++) {
        unis[i].deadCnt++;
        // dead가 많이 쌓이면 dirty 표시
        if (unis[i].deadCnt > N) {
            unis[i].dirty = true;
        }
    }
}


// ---------------- Top-K 재계산 ----------------
void rebuildTopK(int u) {
    auto& uv = unis[u];
    auto& v = uv.all;
    
    // Garbage Collection: dead 학생이 많으면 정리
    int size = v.size();
    if (uv.deadCnt * 100 >= size * GC_THRESHOLD) {
        auto it = remove_if(v.begin(), v.end(), 
            [](const Data& d) { return dead[d.id]; });
        v.erase(it, v.end());
        uv.deadCnt = 0;
        size = v.size();
    }
    
    if (size == 0) {
        uv.topKSize = 0;
        uv.dirty = false;
        return;
    }
    
    // Top-K 정렬
    int K = min(N, size);
    partial_sort(v.begin(), v.begin() + K, v.end(),
        [](const Data& a, const Data& b) { return a > b; });
    
    // Top-K ID만 추출하여 캐시
    uv.topKSize = 0;
    for (int i = 0; i < K && uv.topKSize < N; i++) {
        if (!dead[v[i].id]) {
            uv.topK[uv.topKSize++] = v[i].id;
        }
    }
    
    uv.dirty = false;
}


// ---------------- suggest ----------------
int suggest(int mID) {
    token++;
    
    for (int u = 0; u < M; u++) {
        // Top-K 갱신이 필요한 경우
        if (unis[u].dirty) {
            rebuildTopK(u);
        }
        
        // 빠른 경로: Top-K 캐시 배열 순회
        int cnt = 0;
        short size = unis[u].topKSize;
        int* arr = unis[u].topK;
        
        for (int i = 0; i < size; i++) {
            int id = arr[i];
            
            // 이미 선택된 학생 스킵
            if (taken[id] == token) continue;
            
            taken[id] = token;
            cnt++;
            
            // mID를 찾으면 즉시 반환
            if (id == mID) {
                return u + 1;
            }
            
            // N명 선택 완료
            if (cnt >= N) break;
        }
    }
    
    return -1;
}

```
