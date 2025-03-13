```cpp
#include <iostream>

using namespace std;

const int MAX_N = 100000; // Maximum number of elements
int parent[MAX_N]; // Parent array
int rank[MAX_N]; // Rank array for union by rank

// **Initialize the DSU structure**
void initialize(int n) {
    for (int i = 0; i < n; i++) {
        parent[i] = i; // Each element is its own parent (self-set)
        rank[i] = 1; // Rank starts as 1
    }
}

// **Find with path compression**
int find(int x) {
    if (parent[x] != x)
        parent[x] = find(parent[x]); // Path compression
    return parent[x];
}

// **Union by rank**
void unionSet(int x, int y) {
    int rootX = find(x);
    int rootY = find(y);

    if (rootX != rootY) {
        if (rank[rootX] > rank[rootY]) {
            parent[rootY] = rootX;
        } else if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        } else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }
    }
}

int main() {
    int n = 10; // Example with 10 elements (0 to 9)
    initialize(n);

    unionSet(1, 2);
    unionSet(2, 3);
    unionSet(4, 5);
    unionSet(6, 7);
    unionSet(5, 6);
    
    cout << "Find(3): " << find(3) << endl; // Should be the same as Find(1) or Find(2)
    cout << "Find(7): " << find(7) << endl; // Should be the same as Find(4), Find(5), Find(6)
    
    return 0;
}
```
