```cpp
#if 1
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <unordered_set>
#include <queue>
#include <set>
using namespace std;

unordered_map<int, int> idMap;
int idCnt;

struct Data {
    int mid, point, time;
    bool operator<(const Data& r)const {
        if (point != r.point) return point < r.point;
        if (time != r.time) return time < r.time;
        return mid < r.mid;
    }
};

struct User {
    unordered_set<int> mid;
    priority_queue<Data> pq;
}user[1002];

struct Movie {
    int genre, point, time;
    bool removed;
}movie[30003];

struct RESULT {
    int cnt, IDs[5];
};

priority_queue<Data> genre[6], total;
int N, time;
void init(int N) {
    ::N = N, idCnt = time = 0, idMap.clear();
    // 초기화필요
}

int getID(int c) {
    return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}

int add(int mID, int mGenre, int mTotal) { // 10,000
    if (idMap.count(mID)) return 0;
    int id = idMap[mID] = idCnt++;
    movie[id] = { mGenre, mTotal, time, false };
    Data tmp = { mID, mTotal, time++ };
    genre[mGenre].push(tmp);
    total.push(tmp);
    return 1;
}

int erase(int mID) { // 1,000
    if (!idMap.count(mID)) return 0;
    int id = getID(mID);
    if (movie[id].removed) return 0;
    movie[id].removed = true;
    return 1;
}

int watch(int uID, int mID, int mRating) { // 30,000
    if (!idMap.count(mID) || user[uID].mid.count(mID)) return 0;
    int id = idMap[mID];
    user[uID].mid.insert(mID);
    user[uID].pq.push({ mID, mRating, time });
    movie[id].point += mRating;
    int g = movie[id].genre;
    Data tmp = { mID, movie[id].point, time++ };
    genre[g].push(tmp);
    total.push(tmp);
    return 1;
}

RESULT suggest(int uID) { // 5,000
    RESULT res;
    int cnt = res.cnt = 0;
    auto& p = user[uID].pq;
    vector<Data> popped;
    while (!p.empty()) {
        Data cur = p.top(); p.pop();
        if (movie[idMap[cur.mid]].removed) continue;
        popped.push_back(cur);
        if (popped.size() == 5) break;
    }
    if (popped.empty()) {
        while (!total.empty()) {
            Data cur = total.top(); total.pop();
            if (movie[idMap[cur.mid]].removed) continue;
            popped.push_back(cur);
            if (popped.size() == 5) break;
        }
        for (Data nx : popped) {
            p.push(nx);
            res.IDs[cnt++] = nx.mid;
        }
        res.cnt = (int)popped.size();
    }
    int g = movie[idMap[popped[0].mid]].genre;
    for (Data nx : popped) p.push(nx);
        
    return res;
}
#endif // 1
```
