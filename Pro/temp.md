#include <cstdio>
#include <cstring>
#include <string>
#include <vector>
#include <queue>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>

struct RESULT_E {
    int success;        // 1 or 0
    char locname[5];    // e.g., "A002", null-terminated
};

struct RESULT_S {
    int cnt;            // 0..5
    char carlist[5][8]; // each car no: "XXYZZZZ" + '\0'
};

// ============ 내부 상태 ============
static int G_N, G_M, G_L;

// 구역별 빈 슬롯 수 및 최소 빈 슬롯 힙
static std::vector<int> freeCount; // size N
static std::vector< std::priority_queue<int, std::vector<int>, std::greater<int>> > freeSlots; // per area

// 주차 중 차량 정보
struct ParkedInfo {
    int area;       // 0..N-1
    int slot;       // 0..M-1
    int start;      // 입차 시각
    int towTime;    // start + L
};

// 견인 차량 정보
struct TowedInfo {
    int towStart;       // 견인 시작 시각 (= park.start + L)
    int parkDuration;   // 견인 전까지 주차한 시간 (= L, 단 규칙상 정확히 L)
};

// 차량번호 -> 상태 맵
static std::unordered_map<std::string, ParkedInfo> parked;
static std::unordered_map<std::string, TowedInfo>  towed;

// 자동 견인을 위한 (towTime, carNo) 최소 힙
struct TowEvent {
    int towTime;
    std::string car;
    bool operator>(const TowEvent& other) const {
        if (towTime != other.towTime) return towTime > other.towTime;
        return car > other.car;
    }
};
static std::priority_queue<TowEvent, std::vector<TowEvent>, std::greater<TowEvent>> towPQ;

// 뒤 4자리별 인덱스: 주차/견인 집합
static std::vector< std::unordered_set<std::string> > parkedBySuf; // size 10000
static std::vector< std::unordered_set<std::string> > towedBySuf;  // size 10000

// ============ 유틸 ============

static inline int suffix4_as_int(const char car[]) {
    // car: "XXYZZZZ"
    return (car[3]-'0')*1000 + (car[4]-'0')*100 + (car[5]-'0')*10 + (car[6]-'0');
}
static inline int suffix4_as_int_s(const std::string& s) {
    return (s[3]-'0')*1000 + (s[4]-'0')*100 + (s[5]-'0')*10 + (s[6]-'0');
}

static inline void make_locname(int area, int slot, char out[5]) {
    out[0] = (char)('A' + area);
    out[1] = (char)('0' + (slot/100)%10);
    out[2] = (char)('0' + (slot/10)%10);
    out[3] = (char)('0' + (slot%10));
    out[4] = '\0';
}

// 자동 견인 처리: 현재 시각까지
static void processTow(int now) {
    while (!towPQ.empty()) {
        TowEvent ev = towPQ.top();
        if (ev.towTime > now) break;
        towPQ.pop();

        auto it = parked.find(ev.car);
        if (it == parked.end()) continue; // 이미 출차/견인 처리된 경우(지연 항목)
        ParkedInfo &pi = it->second;
        if (pi.towTime != ev.towTime) continue; // 갱신 불일치(지연 항목)

        // 슬롯을 비움(즉시 재사용 가능)
        freeSlots[pi.area].push(pi.slot);
        freeCount[pi.area]++;

        // parked 목록/인덱스에서 제거
        int suf = suffix4_as_int_s(ev.car);
        parkedBySuf[suf].erase(ev.car);

        // towed에 기록
        TowedInfo ti;
        ti.towStart = ev.towTime;
        ti.parkDuration = pi.towTime - pi.start; // 보통 L
        towed[ev.car] = ti;
        towedBySuf[suf].insert(ev.car);

        parked.erase(it);
    }
}

// 구역 선택: 빈 슬롯이 가장 많은 구역 (동률이면 알파벳 빠른 구역)
static int chooseArea() {
    int bestArea = -1, bestFree = -1;
    for (int a=0; a<G_N; ++a) {
        int fc = freeCount[a];
        if (fc > bestFree) {
            bestFree = fc; bestArea = a;
        } else if (fc == bestFree && fc > 0 && a < bestArea) {
            // 동률 + 빈칸 있음 + 알파벳 앞서면
            bestArea = a;
        }
    }
    if (bestFree <= 0) return -1;
    return bestArea;
}

// 검색 우선순위 정렬용 키
struct Key {
    bool isParked; // true가 우선
    int XX;        // s[0..1] 숫자 (작을수록 우선)
    char Y;        // s[2] (알파벳 빠를수록 우선)
    std::string car;
};
static bool keyLess(const Key& a, const Key& b){
    if (a.isParked != b.isParked) return a.isParked > b.isParked; // parked 우선
    if (a.XX != b.XX) return a.XX < b.XX;
    if (a.Y  != b.Y ) return a.Y  < b.Y;
    return a.car < b.car; // tie-breaker 안정성
}

// ============ API 구현 ============

void init(int N, int M, int L)
{
    G_N = N; G_M = M; G_L = L;

    freeCount.assign(N, 0);
    freeSlots.assign(N, {});
    for (int a=0;a<N;++a) {
        // 모든 슬롯을 빈 슬롯 힙에 push
        std::priority_queue<int, std::vector<int>, std::greater<int>> empty;
        freeSlots[a].swap(empty);
        for (int s=0;s<M;++s) freeSlots[a].push(s);
        freeCount[a] = M;
    }

    parked.clear();
    towed.clear();
    while (!towPQ.empty()) towPQ.pop();

    parkedBySuf.assign(10000, {});
    towedBySuf.assign(10000, {});
}

RESULT_E enter(int mTime, char mCarNo[])
{
    processTow(mTime);

    // 견인 기록이 있으면 주차 성공 여부와 무관하게 삭제
    auto itT = towed.find(std::string(mCarNo));
    if (itT != towed.end()) {
        int suf = suffix4_as_int(mCarNo);
        towedBySuf[suf].erase(std::string(mCarNo));
        towed.erase(itT);
    }

    RESULT_E r; r.success = 0; r.locname[0] = '\0';

    int area = chooseArea();
    if (area == -1) {
        r.success = 0;
        return r;
    }
    // 해당 구역의 가장 앞선 빈 슬롯
    int slot = freeSlots[area].top(); freeSlots[area].pop();
    freeCount[area]--;

    // 주차 기록
    ParkedInfo pi;
    pi.area = area;
    pi.slot = slot;
    pi.start = mTime;
    pi.towTime = mTime + G_L;

    std::string car = std::string(mCarNo);
    parked[car] = pi;

    // 자동 견인 이벤트 등록
    towPQ.push(TowEvent{ pi.towTime, car });

    // suffix 인덱스 반영
    int suf = suffix4_as_int(mCarNo);
    parkedBySuf[suf].insert(car);

    // 결과
    r.success = 1;
    make_locname(area, slot, r.locname);
    return r;
}

int pullout(int mTime, char mCarNo[])
{
    processTow(mTime);

    std::string car = std::string(mCarNo);

    // 주차 중인 경우
    auto itP = parked.find(car);
    if (itP != parked.end()) {
        ParkedInfo &pi = itP->second;

        // 주차 기간
        int duration = mTime - pi.start;

        // 슬롯 반환
        freeSlots[pi.area].push(pi.slot);
        freeCount[pi.area]++;

        // 인덱스/맵 정리
        int suf = suffix4_as_int(mCarNo);
        parkedBySuf[suf].erase(car);
        parked.erase(itP);

        return duration;
    }

    // 견인된 경우
    auto itT = towed.find(car);
    if (itT != towed.end()) {
        TowedInfo ti = itT->second;
        int parkDur = ti.parkDuration;          // 보통 L
        int towDur  = mTime - ti.towStart;      // 견인된 기간
        int value   = -(parkDur + towDur * 5);  // 음수

        // 기록 삭제(사용자 인지로 간주)
        int suf = suffix4_as_int(mCarNo);
        towedBySuf[suf].erase(car);
        towed.erase(itT);

        return value;
    }

    // 없는 경우
    return -1;
}

RESULT_S search(int mTime, char mStr[])
{
    processTow(mTime);

    RESULT_S res; res.cnt = 0;

    // suffix
    int suf = (mStr[0]-'0')*1000 + (mStr[1]-'0')*100 + (mStr[2]-'0')*10 + (mStr[3]-'0');

    std::vector<Key> cand; cand.reserve(64);

    // 주차 중
    for (const auto &s : parkedBySuf[suf]) {
        Key k;
        k.isParked = true;
        k.XX = (s[0]-'0')*10 + (s[1]-'0');
        k.Y  = s[2];
        k.car = s;
        cand.push_back(k);
    }
    // 견인
    for (const auto &s : towedBySuf[suf]) {
        Key k;
        k.isParked = false;
        k.XX = (s[0]-'0')*10 + (s[1]-'0');
        k.Y  = s[2];
        k.car = s;
        cand.push_back(k);
    }

    // 우선순위 정렬
    std::sort(cand.begin(), cand.end(), keyLess);

    // 최대 5대
    int K = (int)std::min<size_t>(5, cand.size());
    res.cnt = K;
    for (int i=0;i<K;++i) {
        std::strncpy(res.carlist[i], cand[i].car.c_str(), 7);
        res.carlist[i][7] = '\0';
    }
    return res;
}
#include <cstdio>
#include <cstring>
#include <vector>
#include <queue>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>

using namespace std;

struct RESULT_E {
    int success;
    char locname[5]; // "A002"
};

struct RESULT_S {
    int cnt;
    char carlist[5][8]; // "XXYZZZZ"
};

// ===== 전역 상태 =====
static int G_N, G_M, G_L;

static vector<int> freeCount; // 구역별 빈 슬롯 수
static vector< priority_queue<int, vector<int>, greater<int>> > freeSlots; // 구역별 최소 슬롯 힙

struct ParkedInfo {
    int area;
    int slot;
    int start;
    int towTime;
};
struct TowedInfo {
    int towStart;
    int parkDuration;
};

// 차량번호를 64비트로 저장
static unordered_map<uint64_t, ParkedInfo> parked;
static unordered_map<uint64_t, TowedInfo>  towed;

struct TowEvent {
    int towTime;
    uint64_t car;
    bool operator>(const TowEvent& o) const {
        if (towTime != o.towTime) return towTime > o.towTime;
        return car > o.car;
    }
};
static priority_queue<TowEvent, vector<TowEvent>, greater<TowEvent>> towPQ;

// 뒤 4자리(0000~9999) → 집합
static vector< unordered_set<uint64_t> > parkedBySuf; // size 10000
static vector< unordered_set<uint64_t> > towedBySuf;  // size 10000

// ===== 유틸 =====
static inline uint64_t encCar(const char s[8]) {
    // 7바이트를 하위 56비트에 패킹 (s[0]..s[6])
    uint64_t x = 0;
    for (int i=0;i<7;i++) x = (x<<8) | (unsigned char)s[i];
    return x;
}
static inline void decCar(uint64_t x, char out[8]) {
    // 상위부터 복원
    for (int i=6;i>=0;i--) { out[i] = (char)(x & 0xFF); x >>= 8; }
    out[7] = '\0';
}
static inline int suffix4_from_car(uint64_t car) {
    // car의 4~7번째 숫자 = s[3..6] (0-based)
    char s[8]; decCar(car, s);
    return (s[3]-'0')*1000 + (s[4]-'0')*100 + (s[5]-'0')*10 + (s[6]-'0');
}
static inline int suffix4_from_str(const char s[5]) {
    return (s[0]-'0')*1000 + (s[1]-'0')*100 + (s[2]-'0')*10 + (s[3]-'0');
}
static inline void make_locname(int area, int slot, char out[5]) {
    out[0] = (char)('A' + area);
    out[1] = (char)('0' + (slot/100)%10);
    out[2] = (char)('0' + (slot/10)%10);
    out[3] = (char)('0' + (slot%10));
    out[4] = '\0';
}

// 자동 견인 (mTime까지)
static inline void processTow(int now) {
    while (!towPQ.empty()) {
        auto ev = towPQ.top();
        if (ev.towTime > now) break;
        towPQ.pop();

        auto it = parked.find(ev.car);
        if (it == parked.end()) continue; // 이미 처리됨
        ParkedInfo &pi = it->second;
        if (pi.towTime != ev.towTime) continue; // 지연 이벤트

        // 슬롯 반환
        freeSlots[pi.area].push(pi.slot);
        freeCount[pi.area]++;

        // parked 인덱스 제거
        int suf = suffix4_from_car(ev.car);
        auto &setP = parkedBySuf[suf];
        setP.erase(ev.car);

        // towed 기록
        TowedInfo ti;
        ti.towStart = ev.towTime;
        ti.parkDuration = pi.towTime - pi.start; // 보통 L
        towed[ev.car] = ti;
        towedBySuf[suf].insert(ev.car);

        parked.erase(it);
    }
}

// 구역 선택
static inline int chooseArea() {
    int bestArea = -1, bestFree = -1;
    // N ≤ 26 → 선형이 가장 빠름
    for (int a=0;a<G_N;++a) {
        int fc = freeCount[a];
        if (fc > bestFree) { bestFree = fc; bestArea = a; }
        else if (fc == bestFree && fc > 0 && a < bestArea) bestArea = a;
    }
    if (bestFree <= 0) return -1;
    return bestArea;
}

// 검색 정렬 키
struct Key {
    bool isParked;
    int XX;
    char Y;
    uint64_t car;
};
static inline bool keyLess(const Key& a, const Key& b){
    if (a.isParked != b.isParked) return a.isParked > b.isParked;
    if (a.XX != b.XX) return a.XX < b.XX;
    if (a.Y  != b.Y ) return a.Y  < b.Y;
    return a.car < b.car;
}

// ===== API =====
void init(int N, int M, int L)
{
    G_N = N; G_M = M; G_L = L;

    freeCount.assign(N, 0);
    freeSlots.assign(N, {});
    for (int a=0;a<N;++a) {
        priority_queue<int, vector<int>, greater<int>> pq;
        freeSlots[a].swap(pq);
        for (int s=0;s<M;++s) freeSlots[a].push(s);
        freeCount[a] = M;
    }

    parked.clear(); towed.clear();
    parked.reserve(4096);
    towed.reserve(4096);
    while (!towPQ.empty()) towPQ.pop();

    parkedBySuf.assign(10000, {});
    towedBySuf.assign(10000, {});
    for (int i=0;i<10000;++i){
        parkedBySuf[i].reserve(16);
        towedBySuf[i].reserve(16);
    }
}

RESULT_E enter(int mTime, char mCarNo[])
{
    processTow(mTime);

    uint64_t car = encCar(mCarNo);

    // 견인 기록 있으면 삭제(성공 여부 무관)
    auto itT = towed.find(car);
    if (itT != towed.end()) {
        int suf = suffix4_from_car(car);
        towedBySuf[suf].erase(car);
        towed.erase(itT);
    }

    RESULT_E r; r.success = 0; r.locname[0] = '\0';

    int area = chooseArea();
    if (area == -1) return r; // 실패

    int slot = freeSlots[area].top(); freeSlots[area].pop();
    freeCount[area]--;

    ParkedInfo pi;
    pi.area = area;
    pi.slot = slot;
    pi.start = mTime;
    pi.towTime = mTime + G_L;

    parked[car] = pi;
    towPQ.push(TowEvent{pi.towTime, car});

    int suf = suffix4_from_car(car);
    parkedBySuf[suf].insert(car);

    r.success = 1;
    make_locname(area, slot, r.locname);
    return r;
}

int pullout(int mTime, char mCarNo[])
{
    processTow(mTime);

    uint64_t car = encCar(mCarNo);

    auto itP = parked.find(car);
    if (itP != parked.end()) {
        ParkedInfo &pi = itP->second;
        int duration = mTime - pi.start;

        freeSlots[pi.area].push(pi.slot);
        freeCount[pi.area]++;

        int suf = suffix4_from_car(car);
        parkedBySuf[suf].erase(car);
        parked.erase(itP);

        return duration;
    }

    auto itT = towed.find(car);
    if (itT != towed.end()) {
        TowedInfo ti = itT->second;
        int parkDur = ti.parkDuration;
        int towDur  = mTime - ti.towStart;
        int value   = -(parkDur + towDur * 5);

        int suf = suffix4_from_car(car);
        towedBySuf[suf].erase(car);
        towed.erase(itT);

        return value;
    }

    return -1;
}

RESULT_S search(int mTime, char mStr[])
{
    processTow(mTime);

    RESULT_S res; res.cnt = 0;

    int suf = suffix4_from_str(mStr);
    vector<Key> cand; cand.reserve(64);

    // 주차 중
    for (auto &c : parkedBySuf[suf]) {
        Key k;
        k.isParked = true;
        char s[8]; decCar(c, s);
        k.XX = (s[0]-'0')*10 + (s[1]-'0');
        k.Y  = s[2];
        k.car = c;
        cand.push_back(k);
    }
    // 견인
    for (auto &c : towedBySuf[suf]) {
        Key k;
        k.isParked = false;
        char s[8]; decCar(c, s);
        k.XX = (s[0]-'0')*10 + (s[1]-'0');
        k.Y  = s[2];
        k.car = c;
        cand.push_back(k);
    }

    sort(cand.begin(), cand.end(), keyLess);

    int K = (int)min<size_t>(5, cand.size());
    res.cnt = K;
    for (int i=0;i<K;++i){
        char s[8]; decCar(cand[i].car, s);
        // 복사
        for (int j=0;j<7;++j) res.carlist[i][j] = s[j];
        res.carlist[i][7] = '\0';
    }
    return res;
}