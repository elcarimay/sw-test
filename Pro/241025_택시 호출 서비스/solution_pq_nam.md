```cpp
#if 1
#include <vector>
#include <queue>
using namespace std;

// inline int abs(int x) { return (x) >= 0 ? (x) : -(x); }
inline int getDist(int x1, int y1, int x2, int y2) { return abs(x1 - x2) + abs(y1 - y2); }
inline int min(int a, int b) { return a < b ? a : b; }
inline int max(int a, int b) { return a < b ? b : a; }

struct Result {
    int mX, mY, mMoveDistance, mRideDistance;
};

struct Taxi {
    int mNo, mX, mY, mMoveDistance, mRideDistance;
    bool operator==(const Taxi& other) const {
        return mNo == other.mNo && mX == other.mX &&
            other.mY && mRideDistance == other.mRideDistance;
    }
    bool operator<(const Taxi& other) const {
        return (mRideDistance < other.mRideDistance) ||
            (mRideDistance == other.mRideDistance && mNo > other.mNo);
    }
};

Taxi taxis[2001];        // Main DB: Taxi ID 1부터 시작
vector<Taxi> taxiGroups[10][10];
priority_queue<Taxi> bestTaxiPQ;

int N;        // 도시의 한 변의 길이 (10 ≤ N ≤ 10,000)
int L;        // 택시가 호출을 받을 수 있는 최대 거리(L = N/10)

//////////////////////////////////////////////////////////////////////////////
void init(int N, int M, int L, int mXs[], int mYs[])
{
    ::N = N, ::L = L;
    for (int i = 0; i < 10; i++)
        for (int j = 0; j < 10; j++)
            taxiGroups[i][j].clear();

    bestTaxiPQ = {};
    for (int i = 0; i < M; i++) {
        int mNo = i + 1;
        taxis[mNo] = { mNo, mXs[i], mYs[i], 0, 0 };
        taxiGroups[mXs[i] / L][mYs[i] / L].push_back(taxis[mNo]);
        bestTaxiPQ.push(taxis[mNo]);
    }
}

int pickup(int mSX, int mSY, int mEX, int mEY)
{
    int res = -1;
    priority_queue<pair<int, int>> taxiPQ;
    int sX = max(0, (mSX - L) / L), eX = min((N - 1) / L, (mSX + L) / L);
    int sY = max(0, (mSY - L) / L), eY = min((N - 1) / L, (mSY + L) / L);

    vector<Taxi> realTaxis;
    for (int i = sX; i <= eX; i++)
        for (int j = sY; j <= eY; j++) {
            realTaxis.clear();
            for (const auto& taxi : taxiGroups[i][j]) {
                int dist = getDist(mSX, mSY, taxi.mX, taxi.mY);
                if (taxi.mX != taxis[taxi.mNo].mX) continue;    // X 위치 확인
                if (taxi.mY != taxis[taxi.mNo].mY) continue;    // Y 위치 확인

                realTaxis.push_back(taxi);
                if (dist <= L) taxiPQ.push({ -dist, -taxi.mNo });
            }
            taxiGroups[i][j] = realTaxis;
        }

    if (!taxiPQ.empty()) {
        auto& taxi = taxis[-taxiPQ.top().second];
        int distToStart = getDist(mSX, mSY, taxi.mX, taxi.mY);
        int distToEnd = getDist(mSX, mSY, mEX, mEY);

        taxi.mX = mEX, taxi.mY = mEY;
        taxi.mMoveDistance += distToStart + distToEnd;
        taxi.mRideDistance += distToEnd;
        taxiGroups[mEX / L][mEY / L].push_back(taxi);
        bestTaxiPQ.push(taxi);
        res = taxi.mNo;
    }
    return res;
}

Result reset(int mNo)
{
    auto& taxi = taxis[mNo];
    Result res = { taxi.mX, taxi.mY, taxi.mMoveDistance, taxi.mRideDistance };
    taxi.mMoveDistance = taxi.mRideDistance = 0;
    bestTaxiPQ.push(taxi);
    return res;
}

void getBest(int mNos[])
{
    vector<Taxi> poppedTaxis;
    int cnt = 0;
    while (!bestTaxiPQ.empty() && cnt < 5) {
        auto taxi = bestTaxiPQ.top(); bestTaxiPQ.pop();
        int mNo = taxi.mNo;

        if (!bestTaxiPQ.empty() && taxi == bestTaxiPQ.top()) continue; // 중복 확인 (TC = 9, 15)
        if (taxi.mX != taxis[mNo].mX) continue;                        // X 위치 확인
        if (taxi.mY != taxis[mNo].mY) continue;                        // Y 위치 확인
        if (taxi.mRideDistance != taxis[mNo].mRideDistance) continue;  // 업데이트 확인
        poppedTaxis.push_back(taxi);
        mNos[cnt++] = taxi.mNo;
    }
    for (const auto& taxi : poppedTaxis) bestTaxiPQ.push(taxi);
}
#endif // 1

```
