```cpp
void dfs(int cur, int parent) {
  for(auto next:adj[cur]){
    if(next == parent) continue;
    dfs(next, cur);
  }
}
```
