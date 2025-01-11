```cpp
#if 1
/*
ver2. segment tree
*/
#include<algorithm>
using namespace std;

const int INF = 1e9;
const int SIZE = 1 << 19;
int N;
int* A;
int maxTree[SIZE], minTree[SIZE], sumTree[SIZE];

void build(int node, int s, int e) {
    if (s == e) {
        maxTree[node] = minTree[node] = sumTree[node] = A[s];
        return;
    }
    int lnode = node * 2;
    int rnode = lnode + 1;
    int mid = (s + e) / 2;
    build(lnode, s, mid);
    build(rnode, mid + 1, e);
    maxTree[node] = max(maxTree[lnode], maxTree[rnode]);
    minTree[node] = min(minTree[lnode], minTree[rnode]);
    sumTree[node] = sumTree[lnode] + sumTree[rnode];
}

void update(int node, int s, int e, int idx) {
    if (s == e) {
        maxTree[node] = minTree[node] = sumTree[node] = A[s];
        return;
    }
    int lnode = node * 2;
    int rnode = lnode + 1;
    int mid = (s + e) / 2;
    if (idx <= mid) update(lnode, s, mid, idx);
    else update(rnode, mid + 1, e, idx);
    maxTree[node] = max(maxTree[lnode], maxTree[rnode]);
    minTree[node] = min(minTree[lnode], minTree[rnode]);
    sumTree[node] = sumTree[lnode] + sumTree[rnode];
}

int qs, qe;
int querySum(int node, int s, int e) {
    if (e < qs || qe < s) return 0;
    if (qs <= s && e <= qe) return sumTree[node];
    int mid = (s + e) / 2;
    return querySum(node * 2, s, mid) + querySum(node * 2 + 1, mid + 1, e);
}

int queryMax(int node, int s, int e) {
    if (e < qs || qe < s) return 0;
    if (qs <= s && e <= qe) return maxTree[node];
    int mid = (s + e) / 2;
    return max(queryMax(node * 2, s, mid), queryMax(node * 2 + 1, mid + 1, e));
}

int queryMin(int node, int s, int e) {
    if (e < qs || qe < s) return INF;
    if (qs <= s && e <= qe) return minTree[node];
    int mid = (s + e) / 2;
    return min(queryMin(node * 2, s, mid), queryMin(node * 2 + 1, mid + 1, e));
}

void init(int N, int mSubscriber[]) {
    ::N = N;
    A = mSubscriber;        // [0, N)
    build(1, 0, N - 1);
}

int subscribe(int mId, int mNum) {
    mId--;
    A[mId] += mNum;
    update(1, 0, N - 1, mId);
    return A[mId];
}

int unsubscribe(int mId, int mNum) {
    return subscribe(mId, -mNum);
}

int count(int sId, int eId) {
    qs = sId - 1, qe = eId - 1;
    return querySum(1, 0, N - 1);
}

int calculate(int sId, int eId) {
    qs = sId - 1, qe = eId - 1;
    return queryMax(1, 0, N - 1) - queryMin(1, 0, N - 1);
}

#endif // 1

```
