```cpp
RESULT show(int mHow, int mCode){
    RESULT res{};                      // 안전한 0 초기화
    vector<Data> tmp;
    tmp.reserve(64);                   // 추정치, 필요시 커짐

    for (int i = 1; i <= 5; i++) {
        if (mHow == 1 && i != mCode) continue; // 카테고리 필터
        for (int j = 1; j <= 5; j++) {
            if (mHow == 2 && j != mCode) continue; // 회사 필터

            int ccSize = (int)catcom[i][j].size();
            int live   = ccSize - removedcatcom[i][j];
            if (live <= 0) continue;

            // 정렬/부분정렬 절대 하지 말고, 살아있는 것만 수집
            for (const Data& nx : catcom[i][j]) {
                int id = nx.id;
                if (!info[id].removed && hmap.count(info[id].mid)) {
                    tmp.push_back({ id });
                }
            }
        }
    }

    if (tmp.empty()) return res;

    // 상위 5개만 고르기: 크기에 따라 sort 또는 partial_sort
    if ((int)tmp.size() <= 5) {
        sort(tmp.begin(), tmp.end());              // Data::< 로 가격→mid 오름차순
    } else {
        partial_sort(tmp.begin(), tmp.begin() + 5, tmp.end());
        tmp.resize(5);
    }

    for (const Data& nx : tmp) {
        res.IDs[res.cnt++] = info[nx.id].mid;
    }
    return res;
}

```
