```cpp
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

void generatePermutations(string& chars, int index) {
    if (index == chars.size()) {
        cout << chars << endl;
        return;
    }

    for (int i = index; i < chars.size(); i++) {
        swap(chars[index], chars[i]);
        generatePermutations(chars, index + 1);
        swap(chars[index], chars[i]); // Backtrack by swapping back
    }
}

int main() {
    string chars = "abcde";
    int r = 3;

    cout << "Combinations of " << r << " elements:\n";
    generateCombinations(chars, r, 0, "");

    cout << "\nPermutions of " << r << " elements:\n";
    generatePermutations(chars, 0);
    return 0;
}
```
