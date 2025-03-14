```cpp
#include <iostream>
using namespace std;

void generateCombinations(const int nums[], int n, int r, int index, int current[], int depth) {
    if (depth == r) { // If we have selected `r` elements
        for (int i = 0; i < r; i++) cout << current[i] << " "; // Print the combination
        cout << endl;
        return;
    }

    for (int i = index; i < n; i++) {
        current[depth] = nums[i]; // Store the selected number
        generateCombinations(nums, n, r, i + 1, current, depth + 1);
    }
}

void generatePermutations(int nums[], int n, int index) {
    if (index == n) { // If a full permutation is formed
        for (int i = 0; i < n; i++) cout << nums[i] << " ";
        cout << endl;
        return;
    }

    for (int i = index; i < n; i++) {
        swap(nums[index], nums[i]); // Swap to fix the current index
        generatePermutations(nums, n, index + 1); // Recurse for the next index
        swap(nums[index], nums[i]); // Backtrack (undo the swap)
    }
}

int main() {
    int nums[5] = { 1, 2, 3, 4, 5 }; // Example input array
    int n = sizeof(nums) / sizeof(nums[0]); // Size of the array
    int r = 3; // Length of each combination
    int current[3]; // Array to store combinations
    cout << "Combinations of " << r << " elements:\n";
    generateCombinations(nums, n, r, 0, current, 0);
    cout << "\nPermutions of " << 5 << " elements:\n";
    generatePermutations(nums, n, 0);
    return 0;
}
```
