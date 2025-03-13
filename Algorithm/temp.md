```cpp
void generateCombinations(const string& chars, int r, int index, string current) {
    if (current.size() == r) {
        cout << current << endl;
        return;
    }

    for (int i = index; i < chars.size(); i++) {
        generateCombinations(chars, r, i + 1, current + chars[i]);
    }
}

please, convert this code to permutation.
```
