```cpp
#if 1
// STL 679 ms (Brute Force 1176 ms)
#include <vector>
using namespace std;

struct Road {
    int time;
    int type;
};
vector<Road> roads;

struct Type {
    vector<int> roadList;
};
vector<Type> types;

//////////////////////////////////////////////////////////////////////////
struct Partition {
    vector<int> buckets;
    vector<int> values;
    int N;    // bucket size

    void init(int num_values) {
        N = sqrt(num_values);
        int num_buckets = ceil((double)num_values / N);
        buckets.clear(); buckets.resize(num_buckets);
        values.clear(); values.resize(num_values);

        // 초기화
        for (int i = 0; i < num_values; i++) {
            values[i] = roads[i].time;
            buckets[i / N] += roads[i].time;
        }
    }
    void update(int idx, int value) {
        buckets[idx / N] -= values[idx];
        values[idx] = value;
        buckets[idx / N] += value;
    }
    int query(int left, int right) {
        int ret = 0;
        int s = left / N;
        int e = right / N;

        if (s == e) {
            for (int i = left; i <= right; i++) { ret += values[i]; }
            return ret;
        }
        while (left / N == s) { ret += values[left++]; }
        while (right / N == e) { ret += values[right--]; }
        for (int i = s + 1; i <= e - 1; i++) { ret += buckets[i]; }

        return ret;
    }
};
Partition part;

//////////////////////////////////////////////////////////////////////////
void init(int N, int M, int mType[], int mTime[])
{
    roads.clear();    roads.resize(N - 1);
    types.clear();    types.resize(M);

    for (int i = 0; i < N - 1; i++) {
        roads[i].type = mType[i];
        roads[i].time = mTime[i];
        types[mType[i]].roadList.push_back(i);
    }
    part.init(N - 1);
}

void destroy() {}

void update(int mID, int mNewTime)
{
    roads[mID].time = mNewTime;
    part.update(mID, mNewTime);
}

int updateByType(int mTypeID, int mRatio256)
{
    int ret = 0;
    for (int rIdx : types[mTypeID].roadList) {
        int temp = roads[rIdx].time * mRatio256 / 256;
        roads[rIdx].time = temp;
        part.update(rIdx, temp);
        ret += roads[rIdx].time;
    }
    return ret;
}

int calculate(int mA, int mB)
{
    int ret = 0;
    if (mA > mB) { swap(mA, mB); }

    //for (int i = mA; i < mB; i++) { ret += roads[i].time; }
    ret = part.query(mA, mB - 1);
    return ret;
}
#endif
```
