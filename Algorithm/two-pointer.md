```cpp
bool twoPointerSum(const vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    
    while (left < right) {
        int sum = arr[left] + arr[right];
        
        if (sum == target) return true; // Found the pair
        else if (sum < target) left++; // Move left pointer to the right
        else right--; // Move right pointer to the left
    }
    
    return false; // No such pair exists
}
```
