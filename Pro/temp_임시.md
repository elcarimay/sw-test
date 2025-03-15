```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

const int MAX_TIME = 5001;

int musicDuration;
int diff[MAX_TIME] = {0};  // Difference array
int activeEmployees[MAX_TIME] = {0};  // Precomputed prefix sum
unordered_map<int, pair<int, int>> employees;  // Stores employees' working times

void init(int mTime) {
    musicDuration = mTime;
    employees.clear();
    fill(begin(diff), end(diff), 0);  // Reset difference array
    fill(begin(activeEmployees), end(activeEmployees), 0);
}

void add(int mid, int mStart, int mEnd) {
    employees[mid] = {mStart, mEnd};
    diff[mStart]++;
    diff[mEnd + 1]--;
}

void remove(int mid) {
    if (employees.count(mid)) {
        auto [mStart, mEnd] = employees[mid];
        diff[mStart]--;
        diff[mEnd + 1]++;
        employees.erase(mid);
    }
}

void preprocess() {
    activeEmployees[0] = diff[0];
    for (int i = 1; i < MAX_TIME; i++) {
        activeEmployees[i] = activeEmployees[i - 1] + diff[i];
    }
}

int getCount(int mStart) {
    int mEnd = mStart + musicDuration;
    if (mEnd >= MAX_TIME) mEnd = MAX_TIME - 1;
    return activeEmployees[mEnd] - activeEmployees[mStart - 1];
}

// Sample Test
int main() {
    init(200);  // Set music duration to 200
    add(1, 100, 500);
    add(2, 300, 700);
    add(3, 600, 900);
    preprocess();  // Compute prefix sums

    cout << getCount(200) << endl;  // Query active employees
    remove(1);
    preprocess();  // Recompute after removal
    cout << getCount(200) << endl;  // Query again

    return 0;
}

```
