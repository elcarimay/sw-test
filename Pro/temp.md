#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <cstdio>
#include <cstring>
#include <vector>
#include <algorithm>
using namespace std;
using ull = unsigned long long;

struct RESULT_E { int success; char locname[5]; };
struct RESULT_S { int cnt; char carlist[5][8]; };

int G_N, G_M, G_L;

struct MinHeap {
    vector<int> a;
    void push(int x) {
        a.push_back(x);
        int i = (int)a.size() - 1;
        while (i > 0) {
            int p = (i - 1) >> 1; if (a[p] <= a[i]) break;
            swap(a[p], a[i]); i = p;
        }
    }
    int pop() {
        int ret = a[0], n = (int)a.size();
        a[0] = a[n - 1]; a.pop_back(); --n;
        int i = 0;
        while (true) {
            int l = i * 2 + 1, r = l + 1, m = i;
            if (l < n && a[l] < a[m]) m = l;
            if (r < n && a[r] < a[m]) m = r;
            if (m == i) break;
            swap(a[i], a[m]); i = m;
        }
        return ret;
    }
};

vector<int> freeCount, nextFree;
vector<MinHeap> holes;

ull encCar(const char s[8]) {
    ull x = 0;
    for (int i = 0; i < 7; i++)
        x = (x << 8) | (unsigned char)s[i];
    return x;
}
void decCar(ull x, char out[8]) {
    for (int i = 6; i >= 0; i--) {
        out[i] = (char)(x & 0xFF);
        x >>= 8;
    }
    out[7] = '\0';
}
int suf4_from_car(ull car) {
    char s[8]; decCar(car, s);
    return (s[3] - '0') * 1000 + (s[4] - '0') * 100 + (s[5] - '0') * 10 + (s[6] - '0');
}
int suf4_from_str(const char s[5]) {
    return (s[0] - '0') * 1000 + (s[1] - '0') * 100 + (s[2] - '0') * 10 + (s[3] - '0');
}
int getXX_from_car(ull car) {
    char s[8]; decCar(car, s);
    return (s[0] - '0') * 10 + (s[1] - '0');
}
char getY_from_car(ull car) {
    char s[8]; decCar(car, s);
    return s[2];
}
void make_locname(int area, int slot, char out[5]) {
    out[0] = (char)('A' + area);
    out[1] = (char)('0' + (slot / 100) % 10);
    out[2] = (char)('0' + (slot / 10) % 10);
    out[3] = (char)('0' + (slot % 10));
    out[4] = '\0';
}

template<typename V>
struct HashMap {
    struct E { ull key; V val; };
    vector<E> tab; int sz, cap;
    ull h(ull x) const {
        x ^= x >> 33; x *= 0xff51afd7ed558ccdULL; x ^= x >> 33; x *= 0xc4ceb9fe1a85ec53ULL; x ^= x >> 33; return x;
    }
    void init(int initCap) { cap = 1; while (cap < initCap) cap <<= 1; tab.assign(cap, { 0ULL,V() }); sz = 0; }
    void rehash() {
        int oc = cap; vector<E> ot = move(tab); cap <<= 1; tab.assign(cap, { 0ULL,V() }); sz = 0;
        for (int i = 0; i < oc; i++) if (ot[i].key) put(ot[i].key, ot[i].val);
    }
    V* find(ull k) {
        if (k == 0ULL) k = 1ULL; ull m = cap - 1, i = h(k) & m;
        while (true) { 
            if (!tab[i].key) return nullptr;
            if (tab[i].key == k) return &tab[i].val; i = (i + 1) & m;
        }
    }
    void put(ull k, const V& v) {
        if (k == 0ULL) k = 1ULL; 
        if ((sz << 1) >= cap) rehash();
        ull m = cap - 1, i = h(k) & m;
        while (true) {
            if (!tab[i].key) { tab[i].key = k; tab[i].val = v; ++sz; return; }
            if (tab[i].key == k) { tab[i].val = v; return; }
            i = (i + 1) & m;
        }
    }
    bool erase(ull k) {
        if (k == 0ULL) k = 1ULL;
        ull m = cap - 1, i = h(k) & m;
        while (true) {
            if (!tab[i].key) return false;
            if (tab[i].key == k) {
                int j = i;
                while (true) {
                    j = (j + 1) & m; if (!tab[j].key) break;
                    unsigned long long h0 = h(tab[j].key) & m;
                    if ((i <= j) ? !(h0 > i && h0 <= j) : !(h0 > i || h0 <= j)) { tab[i] = tab[j]; i = j; }
                }
                tab[i].key = 0ULL; --sz; return true;
            }
            i = (i + 1) & m;
        }
    }
};

struct ParkedInfo { int area, slot, start, towTime, sufIdx; };
struct TowedInfo { int towStart, parkDuration, sufIdx; };
HashMap<ParkedInfo> parked;
HashMap<TowedInfo>  towed;

vector<vector<ull>> parkedVec; // [10000]
vector<vector<ull>> towedVec;  // [10000]

struct TowEvent { int towTime; ull car; };
vector<TowEvent> towHeap;
bool towLess(const TowEvent& a, const TowEvent& b) { return (a.towTime != b.towTime) ? a.towTime < b.towTime : a.car < b.car; }
void towPush(const TowEvent& e) {
    towHeap.push_back(e); 
    int i = (int)towHeap.size() - 1;
    while (i > 0) { int p = (i - 1) >> 1; 
    if (!towLess(towHeap[i], towHeap[p])) break; swap(towHeap[i], towHeap[p]); i = p; }
}
bool towEmpty() { return towHeap.empty(); }
TowEvent towTop() { return towHeap[0]; }
void towPop() {
    int n = (int)towHeap.size(); towHeap[0] = towHeap[n - 1]; towHeap.pop_back(); --n; int i = 0;
    while (true) {
        int l = i * 2 + 1, r = l + 1, m = i;
        if (l < n && towLess(towHeap[l], towHeap[m])) m = l;
        if (r < n && towLess(towHeap[r], towHeap[m])) m = r;
        if (m == i) break; 
        swap(towHeap[i], towHeap[m]); i = m; 
    }
}

int chooseArea() {
    int best = -1, bf = -1;
    for (int a = 0; a < G_N; ++a) {
        int f = freeCount[a];
        if (f > bf) { bf = f; best = a; }
        else if (f == bf && f > 0 && a < best) { best = a; }
    }
    return (bf <= 0) ? -1 : best;
}

int allocSlot(int area) {
    if (!holes[area].a.empty()) { int s = holes[area].pop(); --freeCount[area]; return s; }
    if (nextFree[area] < G_M) { int s = nextFree[area]++; --freeCount[area]; return s; }
    return -1;
}

void freeSlot(int area, int slot) { holes[area].push(slot); ++freeCount[area]; }

void parkedEraseFromSuffix(int suf, int idx) {
    auto& v = parkedVec[suf]; int last = (int)v.size() - 1;
    if (idx != last) { unsigned long long moved = v[last]; v[idx] = moved; if (ParkedInfo* p = parked.find(moved)) p->sufIdx = idx; }
    v.pop_back();
}
void towedEraseFromSuffix(int suf, int idx) {
    auto& v = towedVec[suf]; int last = (int)v.size() - 1;
    if (idx != last) { unsigned long long moved = v[last]; v[idx] = moved; if (TowedInfo* t = towed.find(moved)) t->sufIdx = idx; }
    v.pop_back();
}

void processTow(int now) {
    while (!towEmpty()) {
        TowEvent ev = towTop(); if (ev.towTime > now) break; towPop();
        ParkedInfo* pi = parked.find(ev.car); if (!pi) continue;
        if (pi->towTime != ev.towTime) continue;
        freeSlot(pi->area, pi->slot);
        int suf = suf4_from_car(ev.car);
        parkedEraseFromSuffix(suf, pi->sufIdx);
        int parkDur = ev.towTime - pi->start;
        parked.erase(ev.car);
        TowedInfo ti; ti.towStart = ev.towTime; ti.parkDuration = parkDur; ti.sufIdx = -1;
        towed.put(ev.car, ti);
        towedVec[suf].push_back(ev.car);
        if (TowedInfo* t = towed.find(ev.car)) t->sufIdx = (int)towedVec[suf].size() - 1;
    }
}

void init(int N, int M, int L) {
    G_N = N; G_M = M; G_L = L;
    freeCount.assign(N, M);
    nextFree.assign(N, 0);
    holes.assign(N, MinHeap()); for (int a = 0; a < N; ++a) holes[a].a.clear();
    parked.init(1 << 16); towed.init(1 << 15);
    parkedVec.assign(10000, {}); towedVec.assign(10000, {});
    towHeap.clear();
}

RESULT_E enter(int mTime, char mCarNo[]) {
    processTow(mTime);
    ull car = encCar(mCarNo);

    if (TowedInfo* t = towed.find(car)) {
        int suf = suf4_from_car(car);
        towedEraseFromSuffix(suf, t->sufIdx);
        towed.erase(car);
    }

    RESULT_E r; r.success = 0; r.locname[0] = '\0';
    int area = chooseArea(); if (area == -1) return r;
    int slot = allocSlot(area); if (slot == -1) return r;

    ParkedInfo pi{ area,slot,mTime,mTime + G_L,-1 };
    parked.put(car, pi);
    int suf = suf4_from_car(car);
    parkedVec[suf].push_back(car);
    if (ParkedInfo* p = parked.find(car)) p->sufIdx = (int)parkedVec[suf].size() - 1;

    towPush(TowEvent{ pi.towTime, car });

    r.success = 1; make_locname(area, slot, r.locname);
    return r;
}

int pullout(int mTime, char mCarNo[]) {
    processTow(mTime);
    ull car = encCar(mCarNo);

    if (ParkedInfo* p = parked.find(car)) {
        int dur = mTime - p->start;
        freeSlot(p->area, p->slot);
        int suf = suf4_from_car(car);
        parkedEraseFromSuffix(suf, p->sufIdx);
        parked.erase(car);
        return dur;
    }
    if (TowedInfo* t = towed.find(car)) {
        int parkDur = t->parkDuration;
        int towDur = mTime - t->towStart;
        int val = -(parkDur + towDur * 5);
        int suf = suf4_from_car(car);
        towedEraseFromSuffix(suf, t->sufIdx);
        towed.erase(car);
        return val;
    }
    return -1;
}

RESULT_S search(int mTime, char mStr[]) {
    processTow(mTime);
    RESULT_S res; res.cnt = 0;

    int suf = suf4_from_str(mStr);

    struct Item { bool isP; int XX; char Y; unsigned long long car; };
    auto better = [](const Item& a, const Item& b) {
        if (a.isP != b.isP) return a.isP && !b.isP;
        if (a.XX != b.XX)   return a.XX < b.XX;
        if (a.Y != b.Y)     return a.Y < b.Y;
        return a.car < b.car;
    };

    Item top[5]; int K = 0;

    auto insertTop = [&](const Item& it) {
        // 찾을 초기 위치: 뒤에서부터 한 칸씩 비교
        int pos = K;
        while (pos > 0 && better(it, top[pos - 1])) { pos--; }
        if (K < 5) {
            for (int i = K; i > pos; --i) top[i] = top[i - 1];
            top[pos] = it; ++K;
        }
        else {
            // K==5: 최하위보다 안 좋으면 버림
            if (!better(it, top[4])) return;
            // pos가 5일 수도 있으니, 최소 4로 clamp
            if (pos > 4) pos = 4;
            for (int i = 4; i > pos; --i) top[i] = top[i - 1];
            top[pos] = it;
        }
    };

    const auto& vp = parkedVec[suf];
    for (ull c : vp) {
        Item it{ true, getXX_from_car(c), getY_from_car(c), c };
        insertTop(it);
    }
    const auto& vt = towedVec[suf];
    for (ull c : vt) {
        Item it{ false, getXX_from_car(c), getY_from_car(c), c };
        insertTop(it);
    }

    res.cnt = K;
    for (int i = 0; i < K; ++i) {
        char s[8]; decCar(top[i].car, s);
        for (int j = 0; j < 7; ++j) res.carlist[i][j] = s[j];
        res.carlist[i][7] = '\0';
    }
    return res;
}
#endif // 1
