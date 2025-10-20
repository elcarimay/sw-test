```cpp
#if 1 // 189 ms
#define _CRT_SECURE_NO_WARNINGS
#include <set>
#include <unordered_map>
#include <algorithm>
using namespace std;

#define ull unsigned long long
int days_per_month[13] = { -1, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };

struct SClass {
    ull start, end;
    int ratio;
}sc[10003];

struct Record {
    ull start, end;
    int cid;
}r[10003];

struct Data {
    int rid;
    bool operator<(const Data& re)const {
        if (r[rid].start != r[re.rid].start) return r[rid].start < r[re.rid].start;
        return rid < re.rid;
    }
};

set<Data> s;
set<Data>::iterator it[10003];
int scCnt, rCnt;
unordered_map<int, int> scmap, rmap;

void init() {
    scCnt = rCnt = 0, scmap.clear(), rmap.clear(), s.clear();
}

void destroy() {}

int getsID(int c) {
    return scmap.count(c) ? scmap[c] : scmap[c] = scCnt++;
}

int getrID(int c) {
    return rmap.count(c) ? rmap[c] : rmap[c] = rCnt++;
}

char delim[] = "/-:";
ull convert(char d[], bool opt, bool opt2 = false) { // 날짜는 “2019 / 01 / 01” 부터 “2029 / 12 / 31” 까지 주어질 수 있다., opt가 true면 시간만 계산
    ull time = 0; int month;
    char* p;
    if (opt) {
        p = strtok(d, delim); time += (atoi(p) - 2019); time *= 365;
        p = strtok(nullptr, delim); month = atoi(p);
        for (int i = 1; i < month; i++) time += days_per_month[i];
        p = strtok(nullptr, delim); time += (atoi(p) - 1); time *= 24;
        if (opt2) { time *= 3600; return time; }
        p = strtok(nullptr, delim); time += atoi(p); time *= 60;
        p = strtok(nullptr, delim); time += atoi(p); time *= 60;
        p = strtok(nullptr, delim); time += atoi(p);
    }
    else {
        p = strtok(d, delim); time += atoi(p); time *= 60;
        p = strtok(nullptr, delim); time += atoi(p); time *= 60;
        p = strtok(nullptr, delim); time += atoi(p);
    }
    return time;
}

void newClass(int mClassId, char mClassBegin[], char mClassEnd[], int mRatio) {
    sc[getsID(mClassId)] = { convert(mClassBegin, false), convert(mClassEnd, false), mRatio };
}

void newRecord(int mRecordId, int mClassId, char mRecordBegin[], char mRecordEnd[]) {
    int rid = getrID(mRecordId);
    r[rid] = { convert(mRecordBegin, true), convert(mRecordEnd, true), getsID(mClassId) };
    it[rid] = s.insert({ rid }).first;
}

void changeRecord(int mRecordId, char mNewBegin[], char mNewEnd[]) {
    int rid = getrID(mRecordId);
    r[rid].start = convert(mNewBegin, true), r[rid].end = convert(mNewEnd, true);
    s.erase(it[rid]);
    it[rid] = s.insert({ rid }).first;
}

int checkAttendance(int mClassId, char mDate[]) {
    int sid = getsID(mClassId);
    ull date = convert(mDate, true, true);
    ull class_s = sc[sid].start + date, class_e = sc[sid].end + date;
    ull cnt = 0, start = 0, end = 0;
    for (auto& nx : s) {
        if (r[nx.rid].cid != sid) continue;
        ull st = r[nx.rid].start, en = r[nx.rid].end;
        if (en <= class_s) continue;
        if (class_e <= st) break;
        if (end <= st) { // 새로운 구간이 나타나면
            cnt += end - start;
            start = max(class_s, st), end = min(class_e, en);
        }
        else end = min(class_e, max(en, end)); // 겹치는 부분의 끝부분만 처리
    }
    cnt += end - start;
    return cnt * 100 / (class_e - class_s) >= sc[sid].ratio;
}

int checkCheating() {
    int cid = 0; ull end = 0;
    for (auto& nx : s) {
        auto& re = r[nx.rid];
        if (end <= re.start) cid = re.cid, end = re.end;
        else if (cid != re.cid) return 1;
        else end = max(re.end, end);
    }
    return 0;
}
#endif // 1
```
