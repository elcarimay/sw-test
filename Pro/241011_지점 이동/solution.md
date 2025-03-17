```cpp
#if 1
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

const int MAX_N = 101;
const int INF = 1e9;

struct City {
    vector<pair<int, int>> roads[MAX_N]; // Roads connected to each spot (destination, distance)
    bool bikeRentals[MAX_N];            // Bike rental locations
}city;

struct State {
    int spot, time, cost, status; // 0: 도보, 1:자전거, 2:택시
    bool operator<(const State& other) const {
        return cost > other.cost;
    }
};

int N;
void init(int N) {
    ::N = N;
    for (int i = 1; i <= N; i++) city.roads[i].clear(), city.bikeRentals[i] = false;
}

void addRoad(int K, int mSpotA[], int mSpotB[], int mDis[]) {
    for (int i = 0; i < K; i++) {
        city.roads[mSpotA[i]].push_back({ mSpotB[i], mDis[i] });
        city.roads[mSpotB[i]].push_back({ mSpotA[i], mDis[i] });
    }
}

void addBikeRent(int mSpot) {
    city.bikeRentals[mSpot] = true;
}

int getMinMoney(int mStartSpot, int mEndSpot, int mMaxTime) {
    int minCost[101][3][501];				// 0이 도보, 1이 자전거 2가 택시	
    for (int i = 1; i <= N; i++) {
        for (int j = 0; j < 3; j++) {
            for (int k = 0; k <= mMaxTime; k++)
                minCost[i][j][k] = INF;
        }

    }
    priority_queue<State> pq;
    pq.push({ mStartSpot, 0, 0, 0 });
    minCost[mStartSpot][0][0] = 0;

    while (!pq.empty()) {
        State cur = pq.top(); pq.pop();
        if (cur.cost > minCost[cur.spot][cur.status][cur.time] || cur.time > mMaxTime) continue;
        if (cur.spot == mEndSpot && (cur.status != 1 || (cur.status == 1 && city.bikeRentals[mEndSpot]))) return minCost[cur.spot][cur.status][cur.time];

        for (auto& road : city.roads[cur.spot]) {
            int nextSpot = road.first, distance = road.second, nextTime, nextCost;

            if (cur.status == 0) { // Walking
                // 도보->도보
                nextTime = cur.time + distance * 17;
                nextCost = cur.cost;
                if (nextTime <= mMaxTime && nextCost < minCost[nextSpot][0][nextTime]) {
                    minCost[nextSpot][0][nextTime] = nextCost;
                    pq.push({ nextSpot, nextTime, nextCost, 0 });
                }

                // 도보->자전거
                nextTime = cur.time + distance * 4;
                nextCost = cur.cost + distance * 4;
                if (city.bikeRentals[cur.spot] && nextTime <= mMaxTime && nextCost < minCost[nextSpot][1][nextTime]) {
                    minCost[nextSpot][1][nextTime] = nextCost;
                    pq.push({ nextSpot, nextTime, nextCost, 1 });
                }

                // 도보->택시
                nextTime = cur.time + distance * 1 + 7;
                nextCost = cur.cost + distance * 19;
                if (nextTime <= mMaxTime && nextCost < minCost[nextSpot][2][nextTime]) {
                    minCost[nextSpot][2][nextTime] = nextCost;
                    pq.push({ nextSpot, nextTime, nextCost, 2 });
                }
            }else if (cur.status == 1) { // Bike
                // 자전거->자전거
                nextTime = cur.time + distance * 4;
                nextCost = cur.cost + distance * 4;
                if (nextTime <= mMaxTime && nextCost < minCost[nextSpot][1][nextTime]) {
                    minCost[nextSpot][1][nextTime] = nextCost;
                    pq.push({ nextSpot, nextTime, nextCost, 1 });
                }

                if (city.bikeRentals[cur.spot]) {
                    // 자전거 ->도보
                    nextTime = cur.time + distance * 17;
                    nextCost = cur.cost;
                    if (nextTime <= mMaxTime && nextCost < minCost[nextSpot][0][nextTime]) {
                        minCost[nextSpot][0][nextTime] = nextCost;
                        pq.push({ nextSpot, nextTime, nextCost, 0 });
                    }
                    // 자전거->택시
                    nextTime = cur.time + distance * 1 + 7;
                    nextCost = cur.cost + distance * 19;
                    if (nextTime <= mMaxTime && nextCost < minCost[nextSpot][2][nextTime]) {
                        minCost[nextSpot][2][nextTime] = nextCost;
                        pq.push({ nextSpot, nextTime, nextCost, 2 });
                    }
                }
                
            }else { // Taxi
                // 택시->도보
                nextTime = cur.time + distance * 17;
                nextCost = cur.cost;
                if (nextTime <= mMaxTime && nextCost < minCost[nextSpot][0][nextTime]) {
                    minCost[nextSpot][0][nextTime] = nextCost;
                    pq.push({ nextSpot, nextTime, nextCost, 0 });
                }

                // 택시->자전거
                nextTime = cur.time + distance * 4;
                nextCost = cur.cost + distance * 4;
                if (city.bikeRentals[cur.spot] && nextTime <= mMaxTime && nextCost < minCost[nextSpot][1][nextTime]) {
                    minCost[nextSpot][1][nextTime] = nextCost;
                    pq.push({ nextSpot, nextTime, nextCost, 1 });
                }

                // 택시->택시
                nextTime = cur.time + distance;
                nextCost = cur.cost + distance * 19;
                if (nextTime <= mMaxTime && nextCost < minCost[nextSpot][2][nextTime]) {
                    minCost[nextSpot][2][nextTime] = nextCost;
                    pq.push({ nextSpot, nextTime, nextCost, 2 });
                }
            }
        }
    }
    // If we can't reach the destination within the time limit
    return -1;
}
#endif // 1

```
