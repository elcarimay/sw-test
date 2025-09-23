```cpp
#if 1 // 320 ms
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;
using ull = unsigned long long;

#define MAXN 30
#define MAXM 10000
#define PARKED 0
#define TOWED 1
#define REMOVED 2

struct RESULT_E { int success; char locname[5]; };
struct RESULT_S { int cnt; char carlist[5][8]; }res;
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
    out[1] = (char)('0' + (slot / 100));
    out[2] = (char)('0' + (slot / 10) % 10);
    out[3] = (char)('0' + (slot % 10));
    out[4] = '\0';
}

struct Info { int area, slot, enterTime, towTime, state; }; // towTime == towStart
unordered_map<ull, Info>  hmap;

struct Data {
    ull car; int time, state;
    bool operator<(const Data& r)const {
        if (state != r.state) return r.state == PARKED;
        if (car != r.car) return car > r.car;
        return time < r.time;
    }
};
priority_queue<Data> pq[MAXM];

struct TowEvent {
    ull car; int towTime, mTime;
    bool operator<(const TowEvent& r)const {
        return towTime > r.towTime;
    }
};
priority_queue<TowEvent> towPQ;

int chooseArea() {
    int idx = -1, space = 0;
    for (int i = 0; i < N; i++) if (freeCount[i] > space) space = freeCount[i], idx = i;
    return idx;
}

int allocSlot(int area) {
    --freeCount[area];
    if (!holes[area].empty()) {
        int cur = holes[area].top(); holes[area].pop();
        return cur;
    }
    if (nextFree[area] < M) return nextFree[area]++;
    return -1;
}

void freeSlot(int area, int slot) {
    holes[area].push(slot);
    ++freeCount[area];
}

void processTow(int now) { // 자동 견인 (mTime까지 처리)
    while (!towPQ.empty() && towPQ.top().towTime <= now) {
        TowEvent cur = towPQ.top(); towPQ.pop();
        if (hmap[cur.car].enterTime != cur.mTime || hmap[cur.car].state == REMOVED) continue;
        freeSlot(hmap[cur.car].area, hmap[cur.car].slot); // 슬롯 반환
        pq[suf4_from_car(cur.car)].push({ cur.car, cur.towTime, hmap[cur.car].state = TOWED });
    }
}

void init(int N, int M, int L) {
    ::N = N, ::M = M, ::L = L, hmap.clear();
    for (int i = 0; i < N; i++) {
        freeCount[i] = M, nextFree[i] = 0;
        while (!holes[i].empty()) holes[i].pop();
    }
    while (!towPQ.empty()) towPQ.pop();
    for (int i = 0; i < MAXM; i++) while (!pq[i].empty()) pq[i].pop();
}

RESULT_E enter(int mTime, char mCarNo[]) {
    RESULT_E r = {};
    processTow(mTime);
    ull car = encCar(mCarNo);
    if(hmap.count(car) && hmap[car].state == TOWED) hmap[car].state = REMOVED;
    int area = chooseArea(); if (area == -1) return r;
    int slot = allocSlot(area); if (slot == -1) return r;
    pq[suf4_from_car(car)].push({ car, mTime, PARKED });
    hmap[car] = { area,slot,mTime,mTime + L, PARKED }; // int area, slot, enterTime, towTime; int state
    towPQ.push({ car, mTime + L, mTime });
    r.success = 1; make_locname(area, slot, r.locname);
    return r;
}

int pullout(int mTime, char mCarNo[]) {
    processTow(mTime);
    ull car = encCar(mCarNo);
    if (!hmap.count(car) || hmap[car].state == REMOVED) return -1;
    Info& h = hmap[car];
    if (h.state == PARKED) {
        freeSlot(h.area, h.slot);
        h.state = REMOVED;
        return mTime - h.enterTime;
    }
    else {
        h.state = REMOVED;
        return -(h.towTime - h.enterTime + (mTime - h.towTime) * 5);
    }
}
RESULT_S search(int mTime, char mStr[]) {
    processTow(mTime);
    int suf = (mStr[0] - '0') * 1000 + (mStr[1] - '0') * 100 + (mStr[2] - '0') * 10 + (mStr[3] - '0'), cnt = 0;
    priority_queue<Data> popped;
    while (!pq[suf].empty() && cnt < 5) {
        auto cur = pq[suf].top(); pq[suf].pop();
        Info& h = hmap[cur.car];
        if (h.state == REMOVED || cur.state != h.state) continue;
        if (h.state == PARKED && cur.time != h.enterTime) continue;
        if (h.state == TOWED && cur.time != h.towTime) continue;
        decCar(cur.car, res.carlist[cnt++]);
        popped.push(cur);
    }
    res.cnt = cnt;
    while (!popped.empty()) { pq[suf].push(popped.top()); popped.pop(); }
    return res;
}
#endif // 1
```
