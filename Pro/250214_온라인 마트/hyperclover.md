```cpp
RESULT show(int mHow, int mCode){
    RESULT res = { 0, '\0' };
    vector<Data> tmp;

    // 필요한 카테고리/회사만 반복
    const int start = mHow == 1? mCode : 1;
    const int end = mHow == 1? 6 : mCode + 2;
    const int fixedParam = mHow == 1? mCode : mCode;

    for (int i = 1; i < 6; ++i) {
        for (int j = 1; j < 6; ++j) {
            if ((mHow == 1 && i!= mCode) || (mHow == 2 && j!= mCode)) 
                continue;

            auto& products = catcom[i][j];
            int validCount = products.size() - removedcatcom[i][j];
            if (validCount == 0) continue;

            // 상위 5개 상품 선별
            if (validCount <= 5) {
                sort(products.begin(), products.end());
            } else {
                partial_sort(products.begin(), 
                            products.begin() + min(validCount, 6), 
                            products.end());
            }

            // 유효한 상품만 임시 벡터에 추가
            for (int k = 0; k < min(validCount, 5); ++k) {
                int id = products[k].id;
                if (!info[id].removed) {
                    tmp.push_back(products[k]);
                }
            }
        }
    }

    // 최종 상위 5개 정렬
    if (tmp.size() > 5) {
        partial_sort(tmp.begin(), tmp.begin() + 5, tmp.end());
    } else if (tmp.size() > 1) {
        sort(tmp.begin(), tmp.end());
    }

    // 결과 저장
    for (int i = 0; i < min(5, (int)tmp.size()); ++i) {
        res.IDs[res.cnt++] = info[tmp[i].id].mid;
    }
    return res;
}

```
