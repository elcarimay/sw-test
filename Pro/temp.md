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