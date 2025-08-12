#include <cstdio>
#include <cstring>
#include <vector>
#include <algorithm>
#include <unordered_map>

using namespace std;

struct RESULT_E { int success; char locname[5]; };
struct RESULT_S { int cnt; char carlist[5][8]; };

static int G_N, G_M, G_L;

// ---------------- 구역/슬롯 관리 ----------------
struct MinHeap {
    vector<int> a;
    inline bool empty() const { return a.empty(); }
    inline void push(int x){
        a.push_back(x);
        int i=(int)a.size()-1;
        while(i>0){
            int p=(i-1)>>1; if(a[p]<=a[i]) break;
            swap(a[p],a[i]); i=p;
        }
    }
    inline int top() const { return a[0]; }
    inline int pop(){
        int ret=a[0], n=(int)a.size();
        a[0]=a[n-1]; a.pop_back(); --n;
        int i=0;
        while(true){
            int l=i*2+1,r=l+1,m=i;
            if(l<n && a[l]<a[m]) m=l;
            if(r<n && a[r]<a[m]) m=r;
            if(m==i) break;
            swap(a[i],a[m]); i=m;
        }
        return ret;
    }
    inline void clear(){ a.clear(); }
};
static vector<int> freeCount, nextFree;
static vector<MinHeap> holes;

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

// ---------------- 상태(STL 해시 사용) ----------------
struct ParkedInfo { int area, slot, start, towTime; int sufIdx; };
struct TowedInfo  { int towStart, parkDuration;     int sufIdx;  };

static unordered_map<unsigned long long, ParkedInfo> parked;
static unordered_map<unsigned long long, TowedInfo>  towed;

// 접미사별 컨테이너: swap-pop O(1)
static vector<vector<unsigned long long>> parkedVec; // [10000]
static vector<vector<unsigned long long>> towedVec;  // [10000]

// 견인 이벤트 힙(최소힙)
struct TowEvent{ int towTime; unsigned long long car; };
static vector<TowEvent> towHeap;
static inline bool towLess(const TowEvent&a,const TowEvent&b){
    return (a.towTime!=b.towTime)? a.towTime<b.towTime : a.car<b.car;
}
static inline void towPush(const TowEvent&e){
    towHeap.push_back(e); int i=(int)towHeap.size()-1;
    while(i>0){ int p=(i-1)>>1; if(!towLess(towHeap[i], towHeap[p])) break; swap(towHeap[i],towHeap[p]); i=p; }
}
static inline bool towEmpty(){ return towHeap.empty(); }
static inline TowEvent towTop(){ return towHeap[0]; }
static inline void towPop(){
    int n=(int)towHeap.size();
    towHeap[0]=towHeap[n-1]; towHeap.pop_back(); --n;
    int i=0;
    while(true){
        int l=i*2+1,r=l+1,m=i;
        if(l<n && towLess(towHeap[l],towHeap[m])) m=l;
        if(r<n && towLess(towHeap[r],towHeap[m])) m=r;
        if(m==i) break;
        swap(towHeap[i],towHeap[m]); i=m;
    }
}

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
    if(!holes[area].empty()){ int s=holes[area].pop(); --freeCount[area]; return s; }
    if(nextFree[area] < G_M){ int s=nextFree[area]++; --freeCount[area]; return s; }
    return -1;
}
static inline void freeSlot(int area,int slot){ holes[area].push(slot); ++freeCount[area]; }

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
    while(!towEmpty()){
        TowEvent ev=towTop(); if(ev.towTime>now) break; towPop();
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
    holes.assign(N, MinHeap()); for(int a=0;a<N;++a) holes[a].clear();

    parked.clear(); towed.clear();
    // 예상치에 맞게 reserve(필요 시 조정)
    parked.reserve(1<<16);
    towed.reserve(1<<15);

    parkedVec.assign(10000, {}); towedVec.assign(10000, {});
    towHeap.clear();
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

    towPush(TowEvent{pi.towTime, car});

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
        freeSlot(p.area, p.slot);
        int suf=suf4_from_car(car);
        parkedEraseFromSuffix(suf, p.sufIdx);
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

    Item top[5]; int K=0;
    auto insertTop = [&](const Item& it){
        int pos=K;
        while(pos>0 && better(it, top[pos-1])) pos--;
        if(K<5){
            for(int i=K;i>pos;--i) top[i]=top[i-1];
            top[pos]=it; ++K;
        }else{
            if(!better(it, top[4])) return;
            if(pos>4) pos=4;
            for(int i=4;i>pos;--i) top[i]=top[i-1];
            top[pos]=it;
        }
    };

    // 주차 중
    const auto& vp=parkedVec[suf];
    for(unsigned long long c: vp){
        Item it{true, getXX_from_car(c), getY_from_car(c), c};
        insertTop(it);
    }
    // 견인
    const auto& vt=towedVec[suf];
    for(unsigned long long c: vt){
        Item it{false, getXX_from_car(c), getY_from_car(c), c};
        insertTop(it);
    }

    res.cnt=K;
    for(int i=0;i<K;++i){
        char s[8]; decCar(top[i].car, s);
        for(int j=0;j<7;++j) res.carlist[i][j]=s[j];
        res.carlist[i][7]='\0';
    }
    return res;
}