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
```
