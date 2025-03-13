```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

// **Condition function: Checks if the current guess X is feasible**
bool isFeasible(int X, const vector<int>& arr) {
    // Example: Find if it's possible to split the array into parts where each part has sum <= X
    int currentSum = 0;
    int count = 1; // Count of parts

    for (int num : arr) {
        if (currentSum + num > X) {
            count++; // Create a new part
            currentSum = num; // Start new part with current number
            if (count > 3) // More than 3 parts, not feasible
                return false;
        } else {
            currentSum += num; // Add to current part
        }
    }

    return true;
}

// **Parametric Search to find the optimal value X**
int parametricSearch(const vector<int>& arr) {
    int left = *max_element(arr.begin(), arr.end()); // The smallest feasible X is the max element
    int right = accumulate(arr.begin(), arr.end(), 0); // The largest possible X is the sum of all elements
    int result = right;

    while (left <= right) {
        int mid = left + (right - left) / 2; // Middle value of the current range

        if (isFeasible(mid, arr)) {
            result = mid; // If mid is feasible, try for a larger X
            right = mid - 1; // Search for a smaller feasible X
        } else {
            left = mid + 1; // Otherwise, try for a larger X
        }
    }

    return result;
}

int main() {
    vector<int> arr = {10, 20, 30, 40, 50}; // Example array
    int optimalX = parametricSearch(arr);

    cout << "Optimal value of X: " << optimalX << endl; // Output the best value of X
    return 0;
}
```
