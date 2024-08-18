```cpp
#if 1
// 관리파견도시 선정: linear search
#include <queue>
#include <math.h>
#include <string.h>
using namespace std;

const int LM = 203;

int N, waitWorker, storage[LM], expected[LM];
bool used[LM]; // 관리가 존재하는지 여부

enum TYPE {
    ADD, // 곡물량 추가
    EXPECT, // 예상 곡물량 추가
    ARRIVE, // 관리 파견된 도시에 도착
    BACK, // 관리 수도로 복귀
    SEND, // 파견 가능한 관리들 파견
};

struct Data
{
    int type, T, cid, cnt; // 상황종류, 발생시각, 발생도시, 곡물량
    bool operator<(const Data& r)const {
        if (T != r.T) return T > r.T;
        return type > r.type;
    }
};

priority_queue<Data> pq;

void init(int N, int M) {
    ::N = N, waitWorker = M;
    pq = {};
    for (int i = 0; i < N; i++)
        storage[i] = expected[i] = used[i] = 0;
}

void update(int tStamp) {
    while (!pq.empty() && pq.top().T <= tStamp) {
        Data cur = pq.top(); pq.pop();
        switch (cur.type) {
        case EXPECT:
            expected[cur.cid] += cur.cnt;
            pq.push({ SEND, cur.T }); break;
        case ADD:
            storage[cur.cid] += cur.cnt; break;
        case ARRIVE:
            pq.push({ BACK, cur.T + cur.cid, cur.cid, storage[cur.cid] });
            expected[cur.cid] -= storage[cur.cid];
            storage[cur.cid] = 0; break;
        case BACK:
            waitWorker++;
            used[cur.cid] = 0;
            storage[0] += cur.cnt;
            pq.push({ SEND, cur.T }); break;
        case SEND:
            while (waitWorker) {
                int cid = 0, maxCnt = 0;
                for (int i = 1; i < N; i++)
                    if (!used[i] && maxCnt < expected[i])
                        cid = i, maxCnt = expected[i];
                if (!cid) break;
                used[cid]++;
                waitWorker--;
                pq.push({ ARRIVE, cur.T + cid, cid });
            }
            break;
        }
    }
}

int order(int tStamp, int mCityA, int mCityB, int mTax) {
    int addT = tStamp + abs(mCityB - mCityA); // 곡물추가시점
    pq.push({ ADD, addT, mCityB, mTax });
    int expectT = max(tStamp, addT - mCityB); // 예상곡물추가시점
    pq.push({ EXPECT, expectT, mCityB, mTax });
    update(tStamp);
    return storage[0];
}

int check(int tStamp) {
    update(tStamp);
    return storage[0];
}

void destroy() {}
#endif
```
