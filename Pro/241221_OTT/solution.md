```cpp
#if 1
// 최근영화를 vector로 검색하고 unordered_map으로 장르 및 total을 update함
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <queue>
using namespace std;

unordered_map<int, int> gMap; // [mid] genre

struct Data {
    int mid, point, time;
};

struct TimeFirst {
    bool operator()(Data& a, Data& b){
        if (a.time != b.time) return a.time > b.time;
        return a.point > b.point;
    }
}tf;

struct PointFirst {
    bool operator()(Data& a, Data& b) {
        if (a.point != b.point) return a.point > b.point;
        return a.time > b.time;
    }
}pf;

struct RESULT {
    int cnt, IDs[5];
};

unordered_map<int, Data> genre[6], user[1002];
int N, time;
void init(int N) {
    ::N = N, time = 0, gMap.clear();
    for (int i = 0; i < 6; i++) genre[i] = {};
    for (int i = 0; i <= N; i++) user[i] = {};
}

int add(int mID, int mGenre, int mTotal) { // 10,000
    if (gMap.count(mID)) return 0;
    gMap[mID] = mGenre;
    genre[mGenre][mID] = {mID, mTotal, time};
    genre[0][mID] = { mID, mTotal, time++ };
    return 1;
}

int erase(int mID) { // 1,000
    if (!gMap.count(mID)) return 0;
    gMap.erase(mID);
    for (int i = 0; i < 6; i++) genre[i].erase(mID);
    for (int i = 0; i <= N; i++) user[i].erase(mID);
    return 1;
}

int watch(int uID, int mID, int mRating) { // 30,000
    if (!gMap.count(mID) || user[uID].count(mID)) return 0;
    user[uID][mID] = {mID, mRating, time++ };
    genre[gMap[mID]][mID].point += mRating;
    genre[0][mID].point += mRating;
    return 1;
}

RESULT suggest(int uID) { // 5,000
    RESULT res;
    int cnt = res.cnt = 0;
    vector<Data> tmp, mList;
    for (auto nx: user[uID]) tmp.push_back(nx.second);
    if (tmp.size() > 5) {
        partial_sort(tmp.begin(), tmp.begin() + 5, tmp.end(), tf);
        tmp.resize(5);
    }else sort(tmp.begin(), tmp.end(), tf);
    sort(tmp.begin(), tmp.end(), pf);
    int G = 0; // user[uID]의 영화목록이 존재하지 않을때
    if (!tmp.empty()) G = gMap[tmp[0].mid]; // user[uID]의 영화목록이 존재할때
    for (auto nx : genre[G]) {
        if (user[uID].count(nx.first)) continue;
        mList.push_back(nx.second);
    }
    if (mList.size() > 5) {
        partial_sort(mList.begin(), mList.begin() + 5, mList.end(), pf);
        mList.resize(5);
    }else sort(mList.begin(), mList.end(), pf);
    for (auto nx : mList) res.IDs[cnt++] = nx.mid;
    res.cnt = cnt;
    return res;
}
#endif // 1
```
