```cpp
#if 1 // 375 ms
#define _CRT_SECURE_NO_WARNINGS
#include <cstring>
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <queue>
using namespace std;
using ull = unsigned long long;
using pui = pair<ull, int>;

#define MAXN 30
#define MAXM 10000

struct RESULT_E { int success; char locname[5]; };
struct RESULT_S { int cnt; char carlist[5][8]; };
int N, M, L, freeCount[MAXN], nextFree[MAXN];
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

struct ParkedInfo { int area, slot, enterTime, towTime; };
struct TowedInfo { int towStart, parkDuration; };
unordered_map<ull, ParkedInfo> parked;
unordered_map<ull, TowedInfo>  towed;
priority_queue<pui, vector<pui>, greater<pui>> parkedPQ[MAXM], towedPQ[MAXM];

struct TowEvent {
    int towTime; ull car; int mTime;
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

void processTow(int now) { // 자동 견인 (mTime까지 처리)
    while (!towPQ.empty() && towPQ.top().towTime <= now) {
        TowEvent cur = towPQ.top(); towPQ.pop();
        if (!parked.count(cur.car)) continue;
        if (parked[cur.car].enterTime != cur.mTime) continue;
        ParkedInfo& pi = parked[cur.car];
        int area = pi.area, slot = pi.slot, et = pi.enterTime, tt = pi.towTime;
        parked.erase(cur.car);
        if (tt != cur.towTime) continue; // 지연 이벤트
        freeSlot(area, slot); // 슬롯 반환
        int suf = suf4_from_car(cur.car); // 주차 인덱스 제거
        int parkDur = cur.towTime - et;
        towed[cur.car] = { cur.towTime, cur.towTime - et }; // 견인 등록
        towedPQ[suf].push(make_pair(cur.car, cur.towTime));
    }
}

void init(int N, int M, int L) {
    ::N = N, ::M = M, ::L = L, parked.clear(), towed.clear();
    for (int i = 0; i < N; i++) {
        freeCount[i] = M, nextFree[i] = 0;
        while (!holes[i].empty()) holes[i].pop();
    }
    while (!towPQ.empty()) towPQ.pop();
    for (int i = 0; i < MAXM; i++) {
        while (!parkedPQ[i].empty()) parkedPQ[i].pop();
        while (!towedPQ[i].empty()) towedPQ[i].pop();
    }
}

RESULT_E enter(int mTime, char mCarNo[]) {
    processTow(mTime);
    ull car = encCar(mCarNo);
    if (towed.count(car)) towed.erase(car);
    RESULT_E r = {};
    int zone = chooseArea(); if (zone == -1) return r;
    int slot = allocSlot(zone); if (slot == -1) return r;
    int suf = suf4_from_car(car);
    parkedPQ[suf].push(make_pair(car, mTime));
    parked[car] = { zone,slot,mTime,mTime + L };
    towPQ.push({ parked[car].towTime, car, mTime });
    r.success = 1; make_locname(zone, slot, r.locname);
    return r;
}

int pullout(int mTime, char mCarNo[]) {
    processTow(mTime);
    ull car = encCar(mCarNo);
    if (parked.count(car)) {
        ParkedInfo& p = parked[car];
        freeSlot(p.area, p.slot);
        int et = p.enterTime;
        parked.erase(car);
        return mTime - et;
    }
    if (towed.count(car)) {
        TowedInfo& t = towed[car];
        int towDur = mTime - t.towStart;
        int pD = t.parkDuration;
        towed.erase(car);
        return -(pD + towDur * 5);
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
    int cnt = 0;
    vector<pui> tmp;
    while (!parkedPQ[suf].empty() && cnt <= 5) {
        auto cur = parkedPQ[suf].top(); parkedPQ[suf].pop();
        if (!parked.count(cur.first)) continue;
        if (cur.second != parked[cur.first].enterTime) continue;
        tmp.push_back(cur);
        cnt++;
    }
    for (auto nx : tmp) parkedPQ[suf].push(nx), cand.push_back({ true, nx.first });
    cnt = 0, tmp.clear();
    while (!towedPQ[suf].empty() && cnt <= 5) {
        auto cur = towedPQ[suf].top(); towedPQ[suf].pop();
        if (!towed.count(cur.first)) continue;
        if (cur.second != towed[cur.first].towStart) continue;
        tmp.push_back(cur);
        cnt++;
    }
    for (auto nx : tmp) towedPQ[suf].push(nx), cand.push_back({ false, nx.first });
    if (cand.size() <= 5) sort(cand.begin(), cand.end(), better);
    else partial_sort(cand.begin(), cand.begin() + 5, cand.end(), better);
    res.cnt = (cand.size() < 5) ? cand.size() : 5;
    for (int i = 0; i < res.cnt; i++) decCar(cand[i].car, res.carlist[i]);
    return res;
}
#endif // 1

```
