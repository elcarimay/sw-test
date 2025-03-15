```cpp
#include <vector>
#include <algorithm>
#include <cmath>

const int MAX_TIME = 5000;
const int BLOCK_SIZE = 71; // sqrt(5000)

int musicDuration;
int timeCount[MAX_TIME + 2];
int blockMin[BLOCK_SIZE];

void init(int mTime) {
    musicDuration = mTime;
    std::fill(timeCount, timeCount + MAX_TIME + 2, 0);
    std::fill(blockMin, blockMin + BLOCK_SIZE, 0);
}

void add(int mid, int mStart, int mEnd) {
    for (int i = mStart; i <= mEnd; ++i) {
        timeCount[i]++;
        blockMin[i / BLOCK_SIZE] = std::min(blockMin[i / BLOCK_SIZE], timeCount[i]);
    }
}

void remove(int mid, int mStart, int mEnd) {
    for (int i = mStart; i <= mEnd; ++i) {
        timeCount[i]--;
        blockMin[i / BLOCK_SIZE] = std::min(blockMin[i / BLOCK_SIZE], timeCount[i]);
    }
}

int getCount(int mStart) {
    int mEnd = mStart + musicDuration;
    if (mEnd > MAX_TIME) return 0;

    int minCount = INT_MAX;
    int startBlock = mStart / BLOCK_SIZE;
    int endBlock = mEnd / BLOCK_SIZE;

    if (startBlock == endBlock) {
        for (int i = mStart; i <= mEnd; ++i)
            minCount = std::min(minCount, timeCount[i]);
    }
    else {
        for (int i = mStart; i < (startBlock + 1) * BLOCK_SIZE; ++i)
            minCount = std::min(minCount, timeCount[i]);

        for (int b = startBlock + 1; b < endBlock; ++b)
            minCount = std::min(minCount, blockMin[b]);

        for (int i = endBlock * BLOCK_SIZE; i <= mEnd; ++i)
            minCount = std::min(minCount, timeCount[i]);
    }

    return minCount;
}

#include <iostream>
using namespace std;
int main() {
    init(230);
    add(1, 100, 300);
    add(2, 200, 400);
    add(3, 150, 360);
    add(4, 380, 450);

    cout << getCount(120) << endl;  // 최적화된 결과 출력: 0

    init(330);
    add(1, 100, 300);
    add(2, 200, 400);
    add(3, 150, 360);
    add(4, 380, 450);

    cout << getCount(120) << endl;  // 최적화된 결과 출력: 0

    init(50);
    add(1, 100, 300);
    add(2, 200, 400);
    add(3, 150, 360);
    add(4, 380, 450);

    cout << getCount(200) << endl;  // 최적화된 결과 출력: 3
}
// Claude 3.7
#include <iostream>
#include <unordered_map>
#include <vector>
#include <algorithm>

using namespace std;

// 전역 변수 선언
int broadcastDuration;
unordered_map<int, pair<int, int>> employees; // {사원ID, {출근시간, 퇴근시간}}

// 구간 인덱싱을 위한 자료구조
vector<pair<int, pair<int, int>>> startTimeSorted; // {출근시간, {퇴근시간, 사원ID}}
vector<pair<int, int>> endTimesForStartTime[5001]; // 각 출근시간별 퇴근시간 목록

// 빠른 조회를 위한 캐시
int cachedCounts[5001]; // 각 시작시간별 캐시된 결과
bool cacheValid[5001];  // 캐시 유효성 플래그

void init(int mTime) {
    broadcastDuration = mTime;
    employees.clear();
    startTimeSorted.clear();

    // 출근시간별 퇴근시간 배열 초기화
    for (int i = 0; i <= 5000; i++) {
        endTimesForStartTime[i].clear();
        cachedCounts[i] = 0;
        cacheValid[i] = false;
    }
}

void add(int mid, int mStart, int mEnd) {
    // 기존 캐시 무효화
    for (int i = 0; i <= 5000; i++) {
        cacheValid[i] = false;
    }

    // 기존에 사원이 있었다면 제거
    auto it = employees.find(mid);
    if (it != employees.end()) {
        int oldStart = it->second.first;
        int oldEnd = it->second.second;

        // 해당 출근시간에서 퇴근시간 제거
        auto& endTimes = endTimesForStartTime[oldStart];
        for (size_t i = 0; i < endTimes.size(); ++i) {
            if (endTimes[i].first == oldEnd && endTimes[i].second == mid) {
                endTimes[i] = endTimes.back();
                endTimes.pop_back();
                break;
            }
        }
    }

    // 새 정보 추가
    employees[mid] = { mStart, mEnd };
    endTimesForStartTime[mStart].push_back({ mEnd, mid });
}

void remove(int mid) {
    // 기존 캐시 무효화
    for (int i = 0; i <= 5000; i++) {
        cacheValid[i] = false;
    }

    auto it = employees.find(mid);
    if (it != employees.end()) {
        int oldStart = it->second.first;
        int oldEnd = it->second.second;

        // 해당 출근시간에서 퇴근시간 제거
        auto& endTimes = endTimesForStartTime[oldStart];
        for (size_t i = 0; i < endTimes.size(); ++i) {
            if (endTimes[i].first == oldEnd && endTimes[i].second == mid) {
                endTimes[i] = endTimes.back();
                endTimes.pop_back();
                break;
            }
        }

        employees.erase(mid);
    }
}

int getCount(int mStart) {
    // 캐시가 유효하면 바로 반환
    if (cacheValid[mStart]) {
        return cachedCounts[mStart];
    }

    int mEnd = mStart + broadcastDuration;
    int count = 0;

    // 모든 출근시간에 대해 검사 (mStart 이하인 출근시간만)
    for (int start = 0; start <= mStart; start++) {
        const auto& endTimes = endTimesForStartTime[start];
        for (const auto& endData : endTimes) {
            int end = endData.first;
            // 퇴근시간이 mEnd 이상인 경우만 카운트
            if (end >= mEnd) {
                count++;
            }
        }
    }

    // 결과 캐싱
    cachedCounts[mStart] = count;
    cacheValid[mStart] = true;

    return count;
}

#include <iostream>
using namespace std;
int main() {
    init(230);
    add(1, 100, 300);
    add(2, 200, 400);
    add(3, 150, 360);
    add(4, 380, 450);

    cout << getCount(120) << endl;  // 최적화된 결과 출력: 0

    init(330);
    add(1, 100, 300);
    add(2, 200, 400);
    add(3, 150, 360);
    add(4, 380, 450);

    cout << getCount(120) << endl;  // 최적화된 결과 출력: 0

    init(50);
    add(1, 100, 300);
    add(2, 200, 400);
    add(3, 150, 360);
    add(4, 380, 450);

    cout << getCount(200) << endl;  // 최적화된 결과 출력: 3
}
```
