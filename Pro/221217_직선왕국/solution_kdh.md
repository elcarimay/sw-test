```cpp
#include<vector>
#include<queue>
using namespace std;

#define MAXO 10000
#define MAXC 200
#define Courtier_returned_to_the_capital 0
#define Grain_is_expected_to_arrive 1
#define Grain_arrived_the_city  2
#define Courtier_arrived_the_city  3

struct City
{
    int grain, expected_grain, CourtierCnt;
}city[MAXC];

int cityCnt;

struct Data
{
    int event, eventTime, cId, grain;
    bool operator<(const Data& r)const {
        return grain < r.grain;
    }
};

priority_queue<Data> pq;

void init(int N, int M){
    pq = {};
    cityCnt = N;
    for (int i = 0; i < N; i++)
        city[i].grain = city[i].expected_grain = city[i].CourtierCnt = 0;
    city[0].CourtierCnt = M;
}

void destroy(){ }

int getRichCity() {
    int cId = -1, max_grain = 0;
    for (int i = 1; i < cityCnt; i++) {
        if (city[i].CourtierCnt > 0 || city[i].expected_grain <= max_grain)
            continue;
        cId = i;
        max_grain = city[i].expected_grain;
    }
    return cId;
}

void sendCourtier(int t) {
    int cId = -1;
    while(city[0].CourtierCnt > 0 && ())
}


int order(int tStamp, int mCityA, int mCityB, int mTax){

    return -1;
}

int check(int tStamp){

    return -1;
}
```
