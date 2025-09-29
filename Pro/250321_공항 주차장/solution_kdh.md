```cpp
#if 1 // 165 ms
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;

unordered_map<int, int> id2idx;
int idCnt, N, capa[10003], extend[1003];

struct Info {
    int start, com, expired, idx; bool TOWED;
}info[50003];

vector<int> parked[13];
struct Data {
    int id, expired;
    bool operator<(const Data& r)const {
        return expired > r.expired;
    }
};
priority_queue<Data> pq[13];

int removed(int date, int id) {
    int remain = 0;
    auto& i = info[id];
    if (i.TOWED) return remain = date - i.expired;
    i.expired += extend[i.com];
    if(i.expired < date) remain = date - i.expired;
    else remain = i.expired - date;
    i.TOWED = true;
    int last = (int)parked[i.com].size() - 1;
    if (i.idx != last) {
        info[parked[i.com][last]].idx = i.idx;
        swap(parked[i.com][last], parked[i.com][i.idx]);
    }
    capa[i.com]++;
    parked[i.com].pop_back();
    return remain;
}

void init(int N, int mCapacity[]) {
    ::N = N, idCnt = 1, id2idx.clear();
    for (int i = 0; i < N; i++) {
        capa[i] = mCapacity[i], parked[i].clear(), extend[i] = 0;
        while (!pq[i].empty()) pq[i].pop();
    }
}

int park(int mDate, int mID, int mCompany, int mPeriod) {
    int id = id2idx[mID] = idCnt++;
    if (!capa[mCompany]) return -1;
    parked[mCompany].push_back(id);
    capa[mCompany]--;
    info[id] = { mDate, mCompany, mDate + mPeriod - 1 - extend[mCompany], (int)parked[mCompany].size() - 1, false };
    pq[mCompany].push({ id,info[id].expired });
    return (int)parked[mCompany].size();
}

int retrieve(int mDate, int mID) {
    return removed(mDate, id2idx[mID]);
}

void buy(int mID, int mPeriod) {
    int id = id2idx[mID];
    if (info[id].TOWED) return;
    pq[info[id].com].push({ id,info[id].expired += mPeriod });
}

void event(int mCompany, int mPeriod) {
    extend[mCompany] += mPeriod;
}

int inspect(int mDate, int mCompany) {
    int cnt = 0;
    while (!pq[mCompany].empty()) {
        auto cur = pq[mCompany].top(); pq[mCompany].pop();
        if (info[cur.id].TOWED) continue;
        if (info[cur.id].expired != cur.expired) continue;
        if (info[cur.id].expired + extend[info[cur.id].com] < mDate) {
            cnt++; removed(mDate, cur.id); continue;
        }
        pq[mCompany].push(cur);
        return cnt;
    }
    return cnt;
}
#endif // 1

```
