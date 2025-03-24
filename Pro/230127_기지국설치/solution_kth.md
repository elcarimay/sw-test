```cpp
/*
* parametric search 시, 배열에 loaction 저장 후 배열로 처리
*/
// map지울때 it = loc.erase(it)를 사용
#include<unordered_map>
#include<map>
using namespace std;

unordered_map<int, int> hmap;    // hmap[id] = location
map<int, int> loc;               // loc[location] = id

void init(int N, int mId[], int mLocation[]) {
    hmap.clear();
    loc.clear();
    for (int i = 0; i < N; i++) {
        hmap[mId[i]] = mLocation[i];
        loc[mLocation[i]] = mId[i];
    }
}

int add(int mId, int mLocation) {
    if (hmap.count(mId)) loc.erase(hmap[mId]);
    hmap[mId] = mLocation;
    loc[mLocation] = mId;
    return hmap.size();
}

int remove(int mStart, int mEnd) {
    auto it = loc.lower_bound(mStart);
    while (it != loc.end() && it->first <= mEnd) {
        hmap.erase(it->second);
        it = loc.erase(it);
    }
    return hmap.size();
}

int n, L[25003];
bool isPossible(int limit, int M) { // 인접한 기지국 거리를 limit 이상으로
    int cnt = 1, l = L[0];          // M개 선택할 수 있으면 return 1
    for (int i = 1; i < n; i++) {
        if (l + limit <= L[i]) {
            cnt++;
            l = L[i];
            if (cnt >= M) return 1;
        }
    }
    return 0;
}

int install(int M) {
    n = 0;
    for (auto p : loc) L[n++] = p.first;

    int s = 1, e = 1e9;
    while (s <= e) {
        int mid = (s + e) / 2;
        if (isPossible(mid, M)) s = mid + 1;    // 조건 만족
        else e = mid - 1;
    }
    return e;
}
```
