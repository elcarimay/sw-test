#include <cstdio>
#include <cstring>
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <queue>
#include <functional>

using namespace std;

struct RESULT_E { int success; char locname[5]; };
struct RESULT_S { int cnt; char carlist[5][8]; };

static int G_N, G_M, G_L;

// ---------------- 구역/슬롯 관리 (STL) ----------------
static vector<int> freeCount, nextFree;
// 반납 슬롯: 각 구역별 최소힙
static vector< priority_queue<int, vector<int>, greater<int>> > holes;

// ---------------- 차량 키/유틸 ----------------
static inline unsigned long long encCar(const char s[8]){
    unsigned long long x=0; for(int i=0;i<7;i++) x=(x<<8)|(unsigned char)s[i]; return x;
}
static inline void decCar(unsigned long long x, char out[8]){
    for(int i=6;i>=0;i--){ out[i]=(char)(x&0xFF); x>>=8; } out[7]='\0';
}
static inline int suf4_from_car(unsigned long long car){
    char s[8]; decCar(car,s);
    return (s[3]-'0')*1000 + (s[4]-'0')*100 + (s[5]-'0')*10 + (s[6]-'0');
}
static inline int suf4_from_str(const char s[5]){
    return (s[0]-'0')*1000 + (s[1]-'0')*100 + (s[2]-'0')*10 + (s[3]-'0');
}
static inline int getXX_from_car(unsigned long long car){
    char s[8]; decCar(car,s);
    return (s[0]-'0')*10 + (s[1]-'0');
}
static inline char getY_from_car(unsigned long long car){
    char s[8]; decCar(car,s);
    return s[2];
}
static inline void make_locname(int area, int slot, char out[5]){
    out[0]=(char)('A'+area);
    out[1]=(char)('0'+(slot/100)%10);
    out[2]=(char)('0'+(slot/10)%10);
    out[3]=(char)('0'+(slot%10));
    out[4]='\0';
}

// ---------------- 상태 (STL 해시) ----------------
struct ParkedInfo { int area, slot, start, towTime; int sufIdx; };
struct TowedInfo  { int towStart, parkDuration;     int sufIdx;  };

static unordered_map<unsigned long long, ParkedInfo> parked;
static unordered_map<unsigned long long, TowedInfo>  towed;

// 접미사별 컨테이너: swap-pop O(1)
static vector<vector<unsigned long long>> parkedVec; // [10000]
static vector<vector<unsigned long long>> towedVec;  // [10000]

// 견인 이벤트 힙 (STL priority_queue)
struct TowEvent{ int towTime; unsigned long long car; };
struct TowCmp {
    bool operator()(const TowEvent& a, const TowEvent& b) const {
        if (a.towTime != b.towTime) return a.towTime > b.towTime; // min-heap by time
        return a.car > b.car;
    }
};
static priority_queue<TowEvent, vector<TowEvent>, TowCmp> towPQ;

// ---------------- 슬롯/구역 ----------------
static inline int chooseArea(){
    int best=-1, bf=-1;
    for(int a=0;a<G_N;++a){
        int f=freeCount[a];
        if(f>bf){ bf=f; best=a; }
        else if(f==bf && f>0 && a<best){ best=a; }
    }
    return (bf<=0)? -1 : best;
}
static inline int allocSlot(int area){
    if(!holes[area].empty()){
        int s=holes[area].top(); holes[area].pop();
        --freeCount[area];
        return s;
    }
    if(nextFree[area] < G_M){
        int s=nextFree[area]++; --freeCount[area];
        return s;
    }
    return -1;
}
static inline void freeSlot(int area,int slot){
    holes[area].push(slot);
    ++freeCount[area];
}

// 접미사 벡터에서 O(1) 삭제
static inline void parkedEraseFromSuffix(int suf,int idx){
    auto &v=parkedVec[suf]; int last=(int)v.size()-1;
    if(idx!=last){
        unsigned long long moved=v[last];
        v[idx]=moved;
        auto it = parked.find(moved);
        if(it!=parked.end()) it->second.sufIdx=idx;
    }
    v.pop_back();
}
static inline void towedEraseFromSuffix(int suf,int idx){
    auto &v=towedVec[suf]; int last=(int)v.size()-1;
    if(idx!=last){
        unsigned long long moved=v[last];
        v[idx]=moved;
        auto it = towed.find(moved);
        if(it!=towed.end()) it->second.sufIdx=idx;
    }
    v.pop_back();
}

// 자동 견인 (mTime까지 처리)
static inline void processTow(int now){
    while(!towPQ.empty() && towPQ.top().towTime <= now){
        TowEvent ev = towPQ.top(); towPQ.pop();

        auto itP = parked.find(ev.car);
        if(itP==parked.end()) continue;
        ParkedInfo &pi = itP->second;
        if(pi.towTime!=ev.towTime) continue; // 지연 이벤트

        // 슬롯 반환
        freeSlot(pi.area, pi.slot);

        // 주차 인덱스 제거
        int suf=suf4_from_car(ev.car);
        parkedEraseFromSuffix(suf, pi.sufIdx);

        int parkDur = ev.towTime - pi.start;
        parked.erase(itP);

        // 견인 등록
        TowedInfo ti; ti.towStart=ev.towTime; ti.parkDuration=parkDur; ti.sufIdx=-1;
        auto ins = towed.emplace(ev.car, ti).first;
        towedVec[suf].push_back(ev.car);
        ins->second.sufIdx = (int)towedVec[suf].size()-1;
    }
}

// ---------------- 공개 API ----------------
void init(int N, int M, int L){
    G_N=N; G_M=M; G_L=L;

    freeCount.assign(N, M);
    nextFree.assign(N, 0);
    holes.assign(N, {}); // 구역별 최소힙 초기화

    parked.clear(); towed.clear();
    // 예상 규모에 맞춰 reserve로 재해시 비용 감소
    parked.reserve(1<<16);
    towed.reserve(1<<15);

    parkedVec.assign(10000, {}); towedVec.assign(10000, {});
    while(!towPQ.empty()) towPQ.pop();
}

RESULT_E enter(int mTime, char mCarNo[]){
    processTow(mTime);

    unsigned long long car=encCar(mCarNo);

    // 견인 기록 있으면(성공 여부 무관) 삭제
    auto itT = towed.find(car);
    if(itT!=towed.end()){
        int suf=suf4_from_car(car);
        towedEraseFromSuffix(suf, itT->second.sufIdx);
        towed.erase(itT);
    }

    RESULT_E r; r.success=0; r.locname[0]='\0';
    int area=chooseArea(); if(area==-1) return r;
    int slot=allocSlot(area); if(slot==-1) return r;

    ParkedInfo pi{area,slot,mTime,mTime+G_L,-1};
    auto ins = parked.emplace(car, pi).first;
    int suf=suf4_from_car(car);
    parkedVec[suf].push_back(car);
    ins->second.sufIdx=(int)parkedVec[suf].size()-1;

    towPQ.push(TowEvent{pi.towTime, car});

    r.success=1; make_locname(area,slot,r.locname);
    return r;
}

int pullout(int mTime, char mCarNo[]){
    processTow(mTime);
    unsigned long long car=encCar(mCarNo);

    auto itP = parked.find(car);
    if(itP!=parked.end()){
        ParkedInfo &p = itP->second;
        int dur = mTime - p.start;
        freeSlot(p->area, p->slot);
        int suf=suf4_from_car(car);
        parkedEraseFromSuffix(suf, p->sufIdx);
        parked.erase(itP);
        return dur;
    }
    auto itT = towed.find(car);
    if(itT!=towed.end()){
        TowedInfo &t = itT->second;
        int parkDur=t.parkDuration;
        int towDur = mTime - t.towStart;
        int val = -(parkDur + towDur*5);
        int suf=suf4_from_car(car);
        towedEraseFromSuffix(suf, t.sufIdx);
        towed.erase(itT);
        return val;
    }
    return -1;
}

RESULT_S search(int mTime, char mStr[]){
    processTow(mTime);
    RESULT_S res; res.cnt=0;

    int suf=suf4_from_str(mStr);

    struct Item{ bool isP; int XX; char Y; unsigned long long car; };
    auto better = [](const Item& a, const Item& b){
        if(a.isP!=b.isP) return a.isP && !b.isP; // 주차중 우선
        if(a.XX!=b.XX)   return a.XX < b.XX;     // XX 오름차순
        if(a.Y!=b.Y)     return a.Y  < b.Y;      // Y 오름차순
        return a.car < b.car;                    // tie-break
    };

    // 후보 수집 (STL 사용: partial_sort로 Top-5)
    vector<Item> cand;
    cand.reserve( (size_t)parkedVec[suf].size() + (size_t)towedVec[suf].size() );

    for (auto c : parkedVec[suf]) cand.push_back( Item{true,  getXX_from_car(c), getY_from_car(c), c} );
    for (auto c : towedVec[suf])  cand.push_back( Item{false, getXX_from_car(c), getY_from_car(c), c} );

    if (cand.size() <= 5) {
        sort(cand.begin(), cand.end(), better);
    } else {
        partial_sort(cand.begin(), cand.begin()+5, cand.end(), better);
        cand.resize(5);
    }

    res.cnt = (int)min<size_t>(5, cand.size());
    for(int i=0;i<res.cnt;++i){
        char s[8]; decCar(cand[i].car, s);
        for(int j=0;j<7;++j) res.carlist[i][j]=s[j];
        res.carlist[i][7]='\0';
    }
    return res;
}