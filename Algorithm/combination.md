```cpp
//1. Optimized Iterative Combination using Bit Masking
//This method generates all possible combinations efficiently using bitwise operations.

#include <iostream>
#include <vector>
using namespace std;

void generateCombinations(const string& chars, int r) {
    int n = chars.size();
    int total = 1 << n; // 2^n subsets

    for (int mask = 0; mask < total; mask++) {
        vector<char> combination;
        int count = 0;

        for (int i = 0; i < n; i++) {
            if (mask & (1 << i)) { // Check if the i-th bit is set
                combination.push_back(chars[i]);
                count++;
            }
        }

        if (count == r) { // Print only combinations of size r
            for (char c : combination) cout << c;
            cout << endl;
        }
    }
}

int main() {
    string chars = "abcde";
    int r = 3; // Choose 3 elements

    cout << "Combinations of " << r << " elements:\n";
    generateCombinations(chars, r);
    return 0;
}

// 2. Optimized Recursive Combination (Backtracking)

#include <iostream>
#include <vector>

using namespace std;

void generateCombinations(const string& chars, int r, int index, string current) {
    if (current.size() == r) {
        cout << current << endl;
        return;
    }

    for (int i = index; i < chars.size(); i++) {
        generateCombinations(chars, r, i + 1, current + chars[i]);
    }
}

int main() {
    string chars = "abcde";
    int r = 3;

    cout << "Combinations of " << r << " elements:\n";
    generateCombinations(chars, r, 0, "");
    return 0;
}

// 3. Optimized Iterative Combination (Lexicographic Order)

#include <iostream>
#include <algorithm>

using namespace std;

void generateCombinations(string chars, int r) {
    string mask(r, 1); // First r elements set to 1
    mask.resize(chars.size(), 0); // Rest set to 0

    do {
        for (int i = 0; i < chars.size(); i++) {
            if (mask[i]) cout << chars[i];
        }
        cout << endl;
    } while (prev_permutation(mask.begin(), mask.end()));
}

int main() {
    string chars = "abcde";
    int r = 3;

    cout << "Combinations of " << r << " elements:\n";
    generateCombinations(chars, r);
    return 0;
}
```
