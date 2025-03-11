```cpp
#if 1
#include <iostream>
#include <vector>
#include <set>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>

#define INF 987654321
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
    possibleHeights1.clear(), possibleHeights2.clear(), possibleHeights3.clear();
    sort(sticks.begin(), sticks.end()); // 정렬 (O(N log N))
    for (int i = 0; i < N; i++) possibleHeights1[sticks[i]].push_back(sticks[i]);
    for (int i = 0; i < N; i++) for (int j = i + 1; j < N; j++) 
        possibleHeights2[sticks[i] + sticks[j]].push_back({ sticks[i], sticks[j] });
    for (int i = 0; i < N; i++) for (int j = i + 1; j < N; j++) for (int k = j + 1; k < N; k++)
        possibleHeights3[sticks[i] + sticks[j] + sticks[k]].push_back({ sticks[i], sticks[j], sticks[k] });
}

// 기둥 2개 만들 수 있는지 확인 (최적화)
bool canMakeTwoPillars() {
    vector<Data3> validCombinations; // 1개 또는 2개의 막대로 만들 수 있는 기둥을 저장
    for (int i = 0; i < N; i++) // 1개 조합
        if (sticks[i] == H) validCombinations.push_back({ sticks[i], -1, -1 });
    for (int i = 0; i < N; i++) { // 2개 조합 (투 포인터 활용)
        for (int j = i + 1; j < N; j++) {
            if (sticks[i] + sticks[j] == H) {
                validCombinations.push_back({ sticks[i], sticks[j], -1 });
            }
        }
    }
    for (int i = 0; i < N; i++) { // 3개 조합
        for (int j = i + 1; j < N; j++) {
            for (int k = j + 1; k < N; k++) {
                if (sticks[i] + sticks[j] + sticks[k] == H) {
                    validCombinations.push_back({ sticks[i], sticks[j], sticks[k]});
                }
            }
        }
    }
    // 두 개의 다른 조합이 있는지 확인 (O(N² log N))
    int size = validCombinations.size();
    for (int i = 0; i < size; i++) {
        for (int j = i + 1; j < size; j++) {
            unordered_set<int> used;
            used.insert(validCombinations[i].a);
            if (validCombinations[i].b != -1 && used.count(validCombinations[j].first)) used.insert(validCombinations[i].second);
            bool valid = true;
            if (used.count(validCombinations[j].first) || (validCombinations[j].second != -1 && used.count(validCombinations[j].second))) {
                valid = false;
            }
            if (valid) return true;
        }
    }
    return false;
}

void init() {
    N = 0;
    sticks.clear();
}

void addBeam(int mLength) {
    N++;
    sticks.push_back(mLength);
    generateCombinations();
}

int requireSingle(int mHeight) {
    H = mHeight, ret = INT_MAX;
    for (int nx : possibleHeights1[H]) ret = min(ret, nx);
    for (Data2 nx : possibleHeights2[H])
        if (nx.a > nx.b) ret = min(ret, nx.a);
        else ret = min(ret, nx.b);
    for (Data3 nx : possibleHeights3[H]) {
        int tmp = nx.a; tmp = max(tmp, nx.b); tmp = max(tmp, nx.c);
        ret = min(ret, tmp);
    }
    return ret == INT_MAX ? -1 : ret;
}

int requireTwin(int mHeight) {
    H = mHeight, ret = INT_MAX;
    bool twoPillar = canMakeTwoPillars();
    return twoPillar == true ? ret : -1;
}
#endif // 0
```
