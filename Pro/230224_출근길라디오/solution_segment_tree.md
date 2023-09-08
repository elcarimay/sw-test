```cpp
#if 1

#include <vector>
using namespace std;

const int MAX = 100'000;
const int MAX_M = 10'000;

int time[MAX + 10]; // 구간의 시간정보
vector<int> type[MAX_M + 10];
int N;
typedef long long ll;

struct Segtree
{
    vector<int> tree;

    void resize(int N) {
        tree.resize(N * 4 + 1);
    }

    // s~e까지 초기화 처리
    ll init(int n, int s, int e) {
        if (s == e) return tree[n] = time[s];
        int m = (s + e) / 2;
        return tree[n] = init(n * 2, s, m) + init(n * 2+1, m+1,e);
    }

    // s~e까지 범위를 갖는 segment tree에서 idx에 해당하는 값을 업데이트 해라
    void update(int n, int s, int e, int idx) {
        /* s~e < idx or idx < s~e */
        if (e < idx || idx < s)
            return;
        
        if (s == idx && idx == e) {
            tree[n] = time[idx];
            return;
        }
        int m = (s + e) / 2;
        update(n * 2, s, m, idx);
        update(n * 2 + 1, m + 1, e, idx);
        tree[n] = tree[n * 2] + tree[n * 2 + 1];
    }

    // s~e까지 범위를 갖는 segment tree에서 left~right에 해당하는 값을 업데이트 해라
    int query(int n, int s, int e, int left, int right) {
        /* s - e < left - right < s - e */
        if(e < left || right < s)
            return 0;
        if (left <= s && e <= right)
            return tree[n];
        int m = (s + e) / 2;
        return query(n * 2, s, m, left, right) +
            query(n * 2 + 1, m + 1, e, left, right);
    }
}segtree;

//////////////////////////////////////////////////////////////////////////

//////////////////////////////////////////////////////////////////////////
void init(int _N, int M, int mType[], int mTime[])
{
    N = _N;
    
    for (int i = 0; i < M; i++)
        type[i].clear();

    for (int i = 0; i < N - 1; i++)
    {
        time[i] = mTime[i];
        type[mType[i]].push_back(i);
    }
    segtree.resize(N);
    segtree.init(1, 0, N - 2);
}

void destroy() {}

void update(int mID, int mNewTime)
{
    time[mID] = mNewTime;
    segtree.update(1, 0, N - 2, mID);
}

int updateByType(int mTypeID, int mRatio256)
{
    int ret = 0;
    for (auto t:type[mTypeID])
    {
        time[t] = time[t] * mRatio256 / 256;
        ret += time[t];
        segtree.update(1, 0, N - 2, t);
    }
    return ret;
}

int calculate(int mA, int mB)
{
    if (mA < mB) {
        return segtree.query(1, 0, N - 2, mA, mB - 1);
    }
    else {
        return segtree.query(1, 0, N - 2, mB, mA - 1);
    }
    return -1;
}
#endif // 0
```
