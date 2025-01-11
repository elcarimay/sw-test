```cpp
/*
ver1. sqrt decomposition
      max, min update시, linear search 최소화
*/
#include<algorithm>
using namespace std;
 
const int INF = 1e9;
const int sq = 500;
int N;
int *A;
int maxB[500], minB[500], sumTree[500];
 
void init(int N, int mSubscriber[]) {
    ::N = N;
    A = mSubscriber;        // [0, N)
    for (int i = 0; i < N; i++) {
        int gid = i / sq;
        if (gid*sq == i) maxB[gid] = sumTree[gid] = 0, minB[gid] = INF;
        maxB[gid] = max(maxB[gid], A[i]);
        minB[gid] = min(minB[gid], A[i]);
        sumTree[gid] += A[i];
    }
}
 
int subscribe(int mId, int mNum) {
    mId--;
    int orgVal = A[mId];
    A[mId] += mNum;
    int gid = mId / sq;
    int l = gid * sq;
    int r = min(l + sq, N);
 
    // 감소
    if (mNum < 0) {
        if(maxB[gid] == orgVal) maxB[gid] = *max_element(A + l, A + r);
        minB[gid] = min(minB[gid], A[mId]);
    }
 
    // 증가
    else {
        maxB[gid] = max(maxB[gid], A[mId]);
        if(minB[gid] == orgVal) minB[gid] = *min_element(A + l, A + r);
    }
 
    sumTree[gid] += mNum;
 
    return A[mId];
}
 
int unsubscribe(int mId, int mNum) {
    return subscribe(mId, -mNum);
}
 
int count(int sId, int eId) {
    int res = 0;
    int l = sId - 1, r = eId - 1;
    while (l <= r && l%sq)  res += A[l++];
    while (l <= r && (r + 1) % sq)  res += A[r--];
    while (l <= r) res += sumTree[l / sq], l += sq;
    return res;
}
 
int calculate(int sId, int eId) {
    int maxv = 0, minv = INF;
    int l = sId - 1, r = eId - 1;
    while (l <= r && l%sq) {
        maxv = max(maxv, A[l]);
        minv = min(minv, A[l++]);
    }
    while (l <= r && (r + 1) % sq) {
        maxv = max(maxv, A[r]);
        minv = min(minv, A[r--]);
    }
    while (l <= r) {
        maxv = max(maxv, maxB[l/sq]);
        minv = min(minv, minB[l/sq]);
        l += sq;
    }
    return maxv - minv;
}
```
