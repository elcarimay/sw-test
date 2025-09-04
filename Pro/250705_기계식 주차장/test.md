#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <cstring>
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <unordered_set>
#include <queue>
using namespace std;
using ull = unsigned long long;

#define MAXN 30
#define MAXM 10000

struct RESULT_E { int success; char locname[5]; };
struct RESULT_S { int cnt; char carlist[5][8]; };

int N, M, L;
int freeCount[MAXN], nextFree[MAXN];
priority_queue<int, vector<int>, greater<int>> holes[MAXN];

// 최적화 1: 인라인 함수로 변경하여 함수 호출 오버헤드 제거
inline ull encCar(const char s[8]) {
    ull x = 0; 
    for (int i = 0; i < 7; i++) x = (x << 8) | (unsigned char)s[i];
    return x;
}

inline void decCar(ull x, char out[8]) {
    for (int i = 6; i >= 0; i--) { 
        out[i] = (char)(x & 0xFF); 
        x >>= 8; 
    } 
    out[7] = '\0';
}

inline int suf4_from_car(ull car) {
    // 최적화 2: 문자열 변환 없이 직접 비트 연산으로 접미사 추출
    int d3 = ((car >> 24) & 0xFF) - '0';
    int d2 = ((car >> 16) & 0xFF) - '0';
    int d1 = ((car >> 8) & 0xFF) - '0';
    int d0 = (car & 0xFF) - '0';
    return d3 * 1000 + d2 * 100 + d1 * 10 + d0;
}

inline void make_locname(int area, int slot, char out[5]) {
    out[0] = (char)('A' + area);
    out[1] = (char)('0' + (slot / 100) % 10);
    out[2] = (char)('0' + (slot / 10) % 10);
    out[3] = (char)('0' + (slot % 10));
    out[4] = '\0';
}

struct ParkedInfo { 
    int area, slot, enterTime, towTime; 
    int sufIdx; 
};

struct TowedInfo { 
    int towStart, parkDuration; 
    int sufIdx; 
};

// 최적화 3: unordered_map의 reserve를 통한 rehashing 최소화
unordered_map<ull, ParkedInfo> parked;
unordered_map<ull, TowedInfo> towed;
vector<ull> parkedVec[MAXM], towedVec[MAXM];

struct TowEvent {
    int towTime; 
    ull car;
    bool operator<(const TowEvent& r) const {
        if (towTime != r.towTime) return towTime > r.towTime;
        return car > r.car; // 최적화 4: 동일 시간일 때 일관된 순서 보장
    }
};
priority_queue<TowEvent> towPQ;

// 최적화 5: 영역 선택을 더 효율적으로
inline int chooseArea() {
    int best = -1, maxFree = -1;
    for (int i = 0; i < N; i++) {
        if (freeCount[i] > maxFree) {
            maxFree = freeCount[i];
            best = i;
        }
    }
    return (maxFree <= 0) ? -1 : best;
}

inline int allocSlot(int area) {
    if (!holes[area].empty()) {
        int s = holes[area].top(); 
        holes[area].pop();
        --freeCount[area];
        return s;
    }
    if (nextFree[area] < M) {
        int s = nextFree[area]++;
        --freeCount[area];
        return s;
    }
    return -1;
}

inline void freeSlot(int area, int slot) {
    holes[area].push(slot);
    ++freeCount[area];
}

// 최적화 6: 벡터에서 O(1) 삭제를 위한 swap-and-pop 최적화
inline void parkedEraseFromSuffix(int suf, int idx) {
    auto& vec = parkedVec[suf];
    if (idx != vec.size() - 1) {
        ull lastCar = vec.back();
        vec[idx] = lastCar;
        auto it = parked.find(lastCar);
        if (it != parked.end()) {
            it->second.sufIdx = idx;
        }
    }
    vec.pop_back();
}

inline void towedEraseFromSuffix(int suf, int idx) {
    auto& vec = towedVec[suf];
    if (idx != vec.size() - 1) {
        ull lastCar = vec.back();
        vec[idx] = lastCar;
        auto it = towed.find(lastCar);
        if (it != towed.end()) {
            it->second.sufIdx = idx;
        }
    }
    vec.pop_back();
}

// 최적화 7: 견인 처리 최적화 - 불필요한 반복 제거
void processTow(int now) {
    while (!towPQ.empty() && towPQ.top().towTime <= now) {
        TowEvent cur = towPQ.top(); 
        towPQ.pop();
        
        auto it = parked.find(cur.car);
        if (it == parked.end()) continue;
        
        ParkedInfo& pi = it->second;
        if (pi.towTime != cur.towTime) continue; // stale event
        
        freeSlot(pi.area, pi.slot);
        
        int suf = suf4_from_car(cur.car);
        parkedEraseFromSuffix(suf, pi.sufIdx);
        
        int parkDuration = cur.towTime - pi.enterTime;
        parked.erase(it);
        
        // 견인 정보 등록
        TowedInfo ti = { cur.towTime, parkDuration, -1 };
        auto insResult = towed.emplace(cur.car, ti);
        auto& towedRef = towedVec[suf];
        towedRef.push_back(cur.car);
        insResult.first->second.sufIdx = towedRef.size() - 1;
    }
}

void init(int N, int M, int L) {
    ::N = N, ::M = M, ::L = L;
    
    // 최적화 8: 해시맵 크기 예약으로 rehashing 최소화
    parked.clear();
    parked.reserve(N * M);
    towed.clear();
    towed.reserve(N * M);
    
    for (int i = 0; i < N; i++) {
        freeCount[i] = M;
        nextFree[i] = 0;
        while (!holes[i].empty()) holes[i].pop();
    }
    
    for (int i = 0; i < MAXM; i++) {
        parkedVec[i].clear();
        towedVec[i].clear();
        // 최적화 9: 벡터 크기 예약
        parkedVec[i].reserve(100);
        towedVec[i].reserve(100);
    }
    
    while (!towPQ.empty()) towPQ.pop();
}

RESULT_E enter(int mTime, char mCarNo[]) {
    processTow(mTime);
    
    ull car = encCar(mCarNo);
    
    // 견인 기록이 있으면 삭제
    auto towedIt = towed.find(car);
    if (towedIt != towed.end()) {
        towedEraseFromSuffix(suf4_from_car(car), towedIt->second.sufIdx);
        towed.erase(towedIt);
    }
    
    RESULT_E result = {};
    
    int area = chooseArea();
    if (area == -1) return result;
    
    int slot = allocSlot(area);
    if (slot == -1) return result;
    
    // 주차 정보 등록
    ParkedInfo pi = { area, slot, mTime, mTime + L, -1 };
    auto insResult = parked.emplace(car, pi);
    
    int suf = suf4_from_car(car);
    parkedVec[suf].push_back(car);
    insResult.first->second.sufIdx = parkedVec[suf].size() - 1;
    
    towPQ.push(TowEvent{ pi.towTime, car });
    
    result.success = 1;
    make_locname(area, slot, result.locname);
    return result;
}

int pullout(int mTime, char mCarNo[]) {
    processTow(mTime);
    
    ull car = encCar(mCarNo);
    
    // 주차된 차량 확인
    auto parkedIt = parked.find(car);
    if (parkedIt != parked.end()) {
        ParkedInfo& p = parkedIt->second;
        freeSlot(p.area, p.slot);
        parkedEraseFromSuffix(suf4_from_car(car), p.sufIdx);
        int duration = mTime - p.enterTime;
        parked.erase(parkedIt);
        return duration;
    }
    
    // 견인된 차량 확인
    auto towedIt = towed.find(car);
    if (towedIt != towed.end()) {
        TowedInfo& t = towedIt->second;
        int towDuration = mTime - t.towStart;
        towedEraseFromSuffix(suf4_from_car(car), t.sufIdx);
        towed.erase(towedIt);
        return -(t.parkDuration + towDuration * 5);
    }
    
    return -1;
}

struct Item { 
    bool isParked; 
    ull car; 
    
    bool operator<(const Item& other) const {
        if (isParked != other.isParked) return isParked > other.isParked; // 주차중 우선
        return car < other.car;
    }
};

RESULT_S search(int mTime, char mStr[]) {
    processTow(mTime);
    
    RESULT_S result = {};
    int suf = (mStr[0] - '0') * 1000 + (mStr[1] - '0') * 100 + 
              (mStr[2] - '0') * 10 + (mStr[3] - '0');
    
    // 최적화 10: 벡터 크기 예약으로 reallocation 방지
    vector<Item> candidates;
    candidates.reserve(parkedVec[suf].size() + towedVec[suf].size());
    
    for (ull car : parkedVec[suf]) {
        candidates.push_back({true, car});
    }
    for (ull car : towedVec[suf]) {
        candidates.push_back({false, car});
    }
    
    // 최적화 11: 5개만 필요하므로 partial_sort 사용
    if (candidates.size() <= 5) {
        sort(candidates.begin(), candidates.end());
    } else {
        partial_sort(candidates.begin(), candidates.begin() + 5, 
                    candidates.end());
    }
    
    result.cnt = min((int)candidates.size(), 5);
    for (int i = 0; i < result.cnt; i++) {
        decCar(candidates[i].car, result.carlist[i]);
    }
    
    return result;
}
#endif // 1 