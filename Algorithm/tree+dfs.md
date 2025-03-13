```cpp
#include <iostream>
#include <stack>
using namespace std;

// Tree representation using arrays
const int MAX_NODES = 100; // Maximum number of nodes in the tree
int values[MAX_NODES];     // Array to store node values
int leftChild[MAX_NODES];  // Array to store indices of left children
int rightChild[MAX_NODES]; // Array to store indices of right children

// Recursive DFS function
void dfsRecursive(int node) {
    if (node == -1) return; // Base case: if the node index is -1, return

    // Process the current node
    cout << values[node] << " ";

    // Recursive calls for left and right children
    dfsRecursive(leftChild[node]);
    dfsRecursive(rightChild[node]);
}

// Iterative DFS function using a stack
void dfsIterative(int root) {
    if (root == -1) return;

    stack<int> s; // Stack to simulate recursion
    s.push(root);

    while (!s.empty()) {
        int node = s.top();
        s.pop();

        // Process the current node
        cout << values[node] << " ";

        // Push right child first so left child is processed first (LIFO order)
        if (rightChild[node] != -1) s.push(rightChild[node]);
        if (leftChild[node] != -1) s.push(leftChild[node]);
    }
}

int main() {
    // Example tree construction
    // Node 0: value = 1, left = 1, right = 2
    values[0] = 1; leftChild[0] = 1; rightChild[0] = 2;
    
    // Node 1: value = 2, left = 3, right = 4
    values[1] = 2; leftChild[1] = 3; rightChild[1] = 4;
    
    // Node 2: value = 3, left = -1, right = -1 (no children)
    values[2] = 3; leftChild[2] = -1; rightChild[2] = -1;
    
    // Node 3: value = 4, left = -1, right = -1 (no children)
    values[3] = 4; leftChild[3] = -1; rightChild[3] = -1;
    
    // Node 4: value = 5, left = -1, right = -1 (no children)
    values[4] = 5; leftChild[4] = -1; rightChild[4] = -1;

    cout << "DFS Recursive: ";
    dfsRecursive(0); // Output: 1 2 4 5 3
    cout << endl;

    cout << "DFS Iterative: ";
    dfsIterative(0); // Output: 1 2 4 5 3
    cout << endl;

    return 0;
}
```
