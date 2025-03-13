```cpp
#include <iostream>
#include <vector>

using namespace std;

// **Iterative Binary Search (O(log N))**
int binarySearchIterative(const vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2; // Avoids overflow

        if (arr[mid] == target) return mid; // Found target
        else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1; // Not found
}

// **Recursive Binary Search (O(log N))**
int binarySearchRecursive(const vector<int>& arr, int left, int right, int target) {
    if (left > right) return -1;

    int mid = left + (right - left) / 2;
    
    if (arr[mid] == target) return mid;
    else if (arr[mid] < target) return binarySearchRecursive(arr, mid + 1, right, target);
    else return binarySearchRecursive(arr, left, mid - 1, target);
}

int main() {
    vector<int> arr = {1, 3, 5, 7, 9, 11, 15}; // Sorted array
    int target = 7;

    // Iterative Search
    int result1 = binarySearchIterative(arr, target);
    cout << "Iterative Search: " << (result1 != -1 ? "Found at index " + to_string(result1) : "Not found") << endl;

    // Recursive Search
    int result2 = binarySearchRecursive(arr, 0, arr.size() - 1, target);
    cout << "Recursive Search: " << (result2 != -1 ? "Found at index " + to_string(result2) : "Not found") << endl;

    return 0;
}
```
