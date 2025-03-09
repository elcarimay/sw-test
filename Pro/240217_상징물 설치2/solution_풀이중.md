```cpp
#include <iostream>
#include <vector>
#include <set>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>

using namespace std;

vector<int> sticks;

struct Data2 {
    int a, b;
};
struct Data3 {
    int a, b, c;
};
unordered_map<int, vector<int>> possibleHeights1; // 가능한 기둥 높이 저장
unordered_map<int, vector<Data2>> possibleHeights2; // 가능한 기둥 높이 저장
unordered_map<int, vector<Data3>> possibleHeights3; // 가능한 기둥 높이 저장
int N, H, ret;

// 가능한 모든 기둥 높이 저장 (최적화된 방식)
void generateCombinations() {
    for (auto nx : possibleHeights1) nx.second.clear();
    for (auto nx : possibleHeights1) nx.second.clear();
    for (auto nx : possibleHeights1) nx.second.clear();
    sort(sticks.begin(), sticks.end()); // 정렬 (O(N log N))

    // 1개 조합
    for (int i = 0; i < N; i++) {
        possibleHeights[0][sticks[i]].insert(sticks[i]);
    }

    // 2개 조합 (투 포인터 활용 가능)
    for (int i = 0; i < N; i++) {
        for (int j = i + 1; j < N; j++) {
            possibleHeights[1][sticks[i] + sticks[j]].insert(sticks[i]);
            possibleHeights[1][sticks[i] + sticks[j]].insert(sticks[j]);
        }
    }

    // 3개 조합
    for (int i = 0; i < N; i++) {
        for (int j = i + 1; j < N; j++) {
            for (int k = j + 1; k < N; k++) {
                possibleHeights[2][sticks[i] + sticks[j] + sticks[k]].insert(sticks[i]);
                possibleHeights[2][sticks[i] + sticks[j] + sticks[k]].insert(sticks[j]);
                possibleHeights[2][sticks[i] + sticks[j] + sticks[k]].insert(sticks[k]);
            }
        }
    }
}

// 기둥 2개 만들 수 있는지 확인 (최적화)
bool canMakeTwoPillars() {
    vector<int> heights;
    sort(heights.begin(), heights.end());

    // 1개 또는 2개의 막대로 만들 수 있는 기둥을 저장
    vector<pair<int, int>> validCombinations;

    // 1개 조합
    for (int i = 0; i < N; i++) {
        if (sticks[i] == H) validCombinations.push_back({ sticks[i], -1 });
    }

    // 2개 조합 (투 포인터 활용)
    for (int i = 0; i < N; i++) {
        for (int j = i + 1; j < N; j++) {
            if (sticks[i] + sticks[j] == H) {
                validCombinations.push_back({ sticks[i], sticks[j] });
            }
        }
    }

    // 3개 조합
    for (int i = 0; i < N; i++) {
        for (int j = i + 1; j < N; j++) {
            for (int k = j + 1; k < N; k++) {
                if (sticks[i] + sticks[j] + sticks[k] == H) {
                    validCombinations.push_back({ sticks[i], sticks[j] });
                }
            }
        }
    }

    // 두 개의 다른 조합이 있는지 확인 (O(N² log N))
    int size = validCombinations.size();
    for (int i = 0; i < size; i++) {
        for (int j = i + 1; j < size; j++) {
            unordered_set<int> used;
            used.insert(validCombinations[i].first);
            if (validCombinations[i].second != -1) used.insert(validCombinations[i].second);
            bool valid = true;
            if (used.count(validCombinations[j].first) || (validCombinations[j].second != -1 && used.count(validCombinations[j].second))) {
                valid = false;
            }
            if (valid) return true;
        }
    }

    return false;
}

void init(){
    N = 0;
    sticks.clear();
}

void addBeam(int mLength){
    N++;
    sticks.push_back(mLength);
}

int requireSingle(int mHeight){
    H = mHeight, ret = INT_MAX;
    generateCombinations();
    for (int i = 0; i < 3; i++) {
        if (!possibleHeights[i][H].empty()) {
            for (auto nx : possibleHeights[i][H]) {
                ret = min(ret, nx);
            }
        }
    }
    return ret == INT_MAX ? -1 : ret;
}

int requireTwin(int mHeight){
    H = mHeight;
    generateCombinations();
    bool twoPillar = canMakeTwoPillars();
    return twoPillar == true ? ret : -1;
}
```
