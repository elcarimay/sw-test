```cpp
#include <cstdio>
using namespace std;

bool visit[3];
int arr[3];
int MM = 3;
void perm(int m, int n) {
	if (n == MM) {
		for (int i = 0; i < MM; i++) printf("%d ", arr[i]);
		printf("\n");
	}
	for (int i = 0; i < MM; i++) {       // 순열
		if (!visit[i]) {
			arr[n] = i;
			visit[i] = true;
			perm(i, n + 1);
			visit[i] = false;
		}
	}
}

void dfs(int lev, int u, int cost){
	if(lev == M){
		if(tab[u][arr[M]])
			ret = min(ret, cost + tab[u][arr[M]]);
		return;
	}
	for(int i = 1;i < M;i++){
		int v = arr[i];
		if(!visited[i] && tab[u][v]){
			visited[i] = 1;
			dfs(lev + 1, v, cost + tab[u][v]);
			visited = 0;
		}
	}
}

int main() {
	perm(0, 0);
	return 0;
}
// Output
// 0 1 2
// 0 2 1
// 1 0 2
// 1 2 0
// 2 0 1
// 2 1 0
```
