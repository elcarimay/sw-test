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
