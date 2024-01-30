```cpp
#include <vector>
using namespace std;

#define MAX_ROADS 100001
#define MAX_TYPES 1000

int sqrt(int n) {
    double x1 = n, x2 = (x1 + n / x1) / 2;
    while (x1 - x2 > 0.01) { x1 = x2, x2 = (x1 + n / x1) / 2;}
    return (int)x2;
}
int ceil(int a, int b) { return (a + b - 1) / b; }
void swap(int& a, int& b) { int temp = a; a = b; b = temp; }

struct Road {
    int mTime;
};

Road roads[MAX_ROADS];
vector<int> roadList[MAX_TYPES];

// Point Update → Range Sum Query
struct Partition {
    int arr[MAX_ROADS], buckets[MAX_ROADS], bSize, bCnt;
    int N;    // bucket size

    void init(int num_roads) {
        N = num_roads;
        bSize = sqrt(N);
        bCnt = ceil((double)N / bSize);
        for (int i = 0; i < N; i++) arr[i] = 0;
        for (int i = 0; i < bCnt; i++) buckets[i] = 0;
    }
    void updatePoint(int idx, int value) {
        buckets[idx / bSize] -= arr[idx];
        arr[idx] = value;
        buckets[idx / bSize] += value;
    }
    int queryRange(int left, int right) {
        int res = 0;
        int s = left / bSize;
        int e = right / bSize;

        if (s == e) { // 같은 버킷내에 존재
            for (int i = left; i <= right; i++) res += arr[i];
            return res;
        }
        for (int i = left; i < (s+1) * bSize; i++) res += arr[i];
        for (int i = s + 1; i <= e - 1; i++) res += buckets[i]; // 완전 포함된 버킷
        for (int i = e * bSize; i <= right; i++) res += arr[i];
        return res;
    }
}part;

void init(int N, int M, int mType[], int mTime[])
{
    for (int i = 0; i < M; i++) roadList[i].clear();

    part.init(N - 1);
    for (int i = 0; i < N - 1; i++) {
        roads[i].mTime= mTime[i];
        roadList[mType[i]].push_back(i);
        part.updatePoint(i, mTime[i]);
    }
}

void destroy() {}

void update(int mID, int mNewTime)
{
    roads[mID].mTime = mNewTime;
    part.updatePoint(mID, mNewTime);
}

int updateByType(int mTypeID, int mRatio256)
{
    int res = 0;
    for (int rIdx : roadList[mTypeID]) {
        int mTime = roads[rIdx].mTime * mRatio256 / 256;
        roads[rIdx].mTime = mTime;
        part.updatePoint(rIdx, mTime);
        res += mTime;
    }
    return res;
}

int calculate(int mA, int mB)
{
    if (mA > mB) swap(mA, mB);
    return part.queryRange(mA, mB - 1);
}
```
