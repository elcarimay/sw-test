```cpp
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
ull encCar(const char s[8]) {
    ull x = 0; for (int i = 0; i < 7; i++) x = (x << 8) | (unsigned char)s[i];
    return x;
}
void decCar(ull x, char out[8]) {
    for (int i = 6; i >= 0; i--) { out[i] = (char)(x & 0xFF); x >>= 8; } out[7] = '\0';
}
int suf4_from_car(ull car) {
    char s[8]; decCar(car, s);
    return (s[3] - '0') * 1000 + (s[4] - '0') * 100 + (s[5] - '0') * 10 + (s[6] - '0');
}
void make_locname(int area, int slot, char out[5]) {
    out[0] = (char)('A' + area);
    out[1] = (char)('0' + (slot / 100) % 10);
    out[2] = (char)('0' + (slot / 10) % 10);
    out[3] = (char)('0' + (slot % 10));
    out[4] = '\0';
}

struct ParkedInfo { int area, slot, enterTime, towTime, sufIdx; };
struct TowedInfo { int towStart, parkDuration, sufIdx; };
unordered_map<ull, ParkedInfo> parked;
unordered_map<ull, TowedInfo>  towed;
vector<ull> parkedVec[MAXM], towedVec[MAXM];

struct TowEvent {
    int towTime; ull car;
    bool operator<(const TowEvent& r)const {
        return towTime > r.towTime;
    }
};
priority_queue<TowEvent> towPQ;

int chooseArea() {
    int best = -1, bf = -1;
    for (int i = 0; i < N; i++) {
        int f = freeCount[i];
        if (f > bf) bf = f, best = i;
        else if (f == bf && f > 0 && i < best) best = i;
    }
    return (bf <= 0) ? -1 : best;
}

int allocSlot(int area) {
    if (!holes[area].empty()) {
        int s = holes[area].top(); holes[area].pop();
        --freeCount[area];
        return s;
    }
    if (nextFree[area] < M) {
        int s = nextFree[area]++; --freeCount[area];
        return s;
    }
    return -1;
}

void freeSlot(int area, int slot) {
    holes[area].push(slot);
    ++freeCount[area];
}

void parkedEraseFromSuffix(int suf, int idx) { // 접미사 벡터에서 O(1) 삭제
    if (idx != parkedVec[suf].size() - 1) {
        auto it = parked.find(parkedVec[suf][idx] = parkedVec[suf][parkedVec[suf].size() - 1]);
        if (it != parked.end()) it->second.sufIdx = idx;
    }
    parkedVec[suf].pop_back();
}

void towedEraseFromSuffix(int suf, int idx) {
    auto& v = towedVec[suf]; int last = v.size() - 1;
    if (idx != last) {
        auto it = towed.find(v[idx] = v[last]);
        if (it != towed.end()) it->second.sufIdx = idx;
    }
    v.pop_back();
}

void processTow(int now) { // 자동 견인 (mTime까지 처리)
    while (!towPQ.empty() && towPQ.top().towTime <= now) {
        TowEvent cur = towPQ.top(); towPQ.pop();
        auto it = parked.find(cur.car);
        if (it == parked.end()) continue;
        ParkedInfo& pi = it->second;
        if (pi.towTime != cur.towTime) continue; // 지연 이벤트
        freeSlot(pi.area, pi.slot); // 슬롯 반환
        int suf = suf4_from_car(cur.car); // 주차 인덱스 제거
        parkedEraseFromSuffix(suf, pi.sufIdx);
        int parkDur = cur.towTime - pi.enterTime;
        parked.erase(it);
        TowedInfo ti = { cur.towTime, cur.towTime - pi.enterTime, -1 }; // 견인 등록
        auto ins = towed.emplace(cur.car, ti).first;
        towedVec[suf].push_back(cur.car);
        ins->second.sufIdx = towedVec[suf].size() - 1;
    }
}

void init(int N, int M, int L) {
    ::N = N, ::M = M, ::L = L, parked.clear(), towed.clear();
    for (int i = 0; i < N; i++) {
        freeCount[i] = M, nextFree[i] = 0;
        while (!holes[i].empty()) holes[i].pop();
    }
    for (int i = 0; i < MAXM; i++) parkedVec[i].clear(), towedVec[i].clear();
    while (!towPQ.empty()) towPQ.pop();
}

RESULT_E enter(int mTime, char mCarNo[]) {
    processTow(mTime);
    ull car = encCar(mCarNo);
    auto it = towed.find(car); // 견인 기록 있으면(성공 여부 무관) 삭제
    if (it != towed.end()) towedEraseFromSuffix(suf4_from_car(car), it->second.sufIdx), towed.erase(it);
    RESULT_E r = {};
    int zone = chooseArea(); if (zone == -1) return r;
    int slot = allocSlot(zone); if (slot == -1) return r;
    ParkedInfo pi = { zone,slot,mTime,mTime + L,-1 };
    auto ins = parked.emplace(car, pi).first;
    int suf = suf4_from_car(car);
    parkedVec[suf].push_back(car);
    ins->second.sufIdx = parkedVec[suf].size() - 1;
    towPQ.push(TowEvent{ pi.towTime, car });
    r.success = 1; make_locname(zone, slot, r.locname);
    return r;
}

int pullout(int mTime, char mCarNo[]) {
    processTow(mTime);
    ull car = encCar(mCarNo);
    auto it = parked.find(car);
    if (it != parked.end()) {
        ParkedInfo& p = it->second;
        freeSlot(p.area, p.slot);
        parkedEraseFromSuffix(suf4_from_car(car), p.sufIdx);
        parked.erase(it);
        return mTime - p.enterTime;
    }
    auto itT = towed.find(car);
    if (itT != towed.end()) {
        TowedInfo& t = itT->second;
        int towDur = mTime - t.towStart;
        towedEraseFromSuffix(suf4_from_car(car), t.sufIdx);
        towed.erase(itT);
        return -(t.parkDuration + towDur * 5);
    }
    return -1;
}

struct Item { bool isP; ull car; };
bool better(const Item& a, const Item& b) {
    if (a.isP != b.isP) return a.isP; // 주차중 우선
    return a.car < b.car;
};
RESULT_S search(int mTime, char mStr[]) {
    processTow(mTime);
    RESULT_S res = {};
    int suf = (mStr[0] - '0') * 1000 + (mStr[1] - '0') * 100 + (mStr[2] - '0') * 10 + (mStr[3] - '0');
    vector<Item> cand; // 후보 수집 (STL 사용: partial_sort로 Top-5)
    for (auto c : parkedVec[suf]) cand.push_back(Item{ true,  c });
    for (auto c : towedVec[suf])  cand.push_back(Item{ false, c });
    if (cand.size() <= 5) sort(cand.begin(), cand.end(), better);
    else partial_sort(cand.begin(), cand.begin() + 5, cand.end(), better);
    res.cnt = (cand.size() < 5) ? cand.size() : 5;
    for (int i = 0; i < res.cnt; i++) {
        char s[8]; decCar(cand[i].car, s);
        for (int j = 0; j < 7; ++j) res.carlist[i][j] = s[j];
        res.carlist[i][7] = '\0';
    }
    return res;
}
#endif // 1

```
