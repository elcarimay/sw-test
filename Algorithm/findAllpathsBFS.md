```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

vector<vector<int>> allPaths;
void findAllPathsBFS(const vector<vector<int>>& graph, int start, int end) {
    queue<vector<int>> q;
    q.push({ start });
    while (!q.empty()) {
        vector<int> path = q.front(); q.pop();
        if (path.back() == end) {
            allPaths.push_back(path); continue;
        }
        for (int next : graph[current]) {
            if (find(path.begin(), path.end(), next) != path.end()) continue; // 방문 체크 (사이클 방지)
            vector<int> newPath = path;
            newPath.push_back(next);
            q.push(newPath);
        }
    }
}
```
