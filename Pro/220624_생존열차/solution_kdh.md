```cpp
#if 1
#include <vector>
#include <set>
using namespace std;

struct Data {
    int mid, point;
    bool operator<(const Data& r)const {
        if (point != r.point) return point > r.point;
        return mid < r.mid;
    }
};

struct People {
    int point, rid;
    set<Data>::iterator it;
}people[100003];

set<Data> room[10];
vector<int> jobMap[1001];
int N, M;
void init(int N, int M, int J, int mPoint[], int mJobID[]) {
    ::N = N; ::M = M;
    //jobMap.clear();
    for (int i = 0; i < J; i++) jobMap[i].clear();
    for (int i = 0; i < N / M; i++) room[i].clear();
    for (int i = 0; i < N; i++) {
        people[i].point = mPoint[i];
        people[i].rid = int(i / M);
        people[i].it = room[int(i / M)].insert({ i,mPoint[i] }).first;
        jobMap[mJobID[i]].push_back(i);
    }
}

void destroy() {}

int update(int mID, int mPoint) {
    room[people[mID].rid].erase(people[mID].it);
    people[mID].it = room[people[mID].rid].insert({ mID, people[mID].point += mPoint }).first;
    return people[mID].point;
}

int updateByJob(int mJobID, int mPoint) {
    int sum = 0;
    for (auto mID : jobMap[mJobID]) {
        update(mID, mPoint);
        sum += people[mID].point;
    }
    return sum;
}

int move(int mNum) {
    int sum = 0, mid;
    vector<int> tmp[10];
    for (int i = 1; i < int(N / M); i++) {
        for (int j = 0; j < mNum; j++) {
            tmp[i].push_back(room[i - 1].rbegin()->mid);
            tmp[i - 1].push_back(room[i].begin()->mid);
            room[i - 1].erase(--room[i - 1].end());
            room[i].erase(room[i].begin());
        }
    }
    for (int i = 0; i < int(N / M); i++) {
        for (auto mid : tmp[i]) {
            people[mid].it = room[i].insert({ mid,people[mid].point }).first;
            people[mid].rid = i;
            sum += people[mid].point;
        }
    }
    return sum;
}
#endif // 1

```
