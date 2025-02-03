```cpp
#include <vector>
#include <algorithm>
#include <string.h>
using namespace std;

#define MAX_MAP_SIZE 2000
int(*map)[MAX_MAP_SIZE];
int update[63][63], N, K;
struct Point {
	int r, c;
	bool operator<(const Point& re)const {
		int a = map[r][c] + update[r / K][c / K];
		int b = map[re.r][re.c] + update[re.r / K][re.c / K];
		if (a != b) return a > b;
		if (r != re.r) return r < re.r;
		return c < re.c;
	}
};

vector<Point> v[63][63];
void init(int N, int K, int mHeight[][MAX_MAP_SIZE]) {
	map = mHeight;
	::N = N, ::K = K;
	memset(update, 0, sizeof(update));
	memset(v, 0, sizeof(v));
	for (int r = 0; r < N / K; r++) for (int c = 0; c < N / K; c++) {
		vector<Point> tmp;
		for (int i = r * K; i < r * K + K; i++)
			for (int j = c * K; j < c * K + K; j++)
				tmp.push_back({ i, j });
		partial_sort(tmp.begin(), tmp.begin() + min(5, K * K), tmp.end());
		int cnt = 1;
		for (auto nx : tmp) {
			v[r][c].push_back(nx);
			if (cnt++ == 5) break;
		}
	}
}

void destroy() {}

void query(Point mA, Point mB, int mCount, Point mTop[]) {
	vector<Point> tmp;
	mA.r--, mA.c--, mB.r--, mB.c--;
	for (int i = mA.r / K; i <= mB.r / K; i++)
		for (int j = mA.c / K; j <= mB.c / K; j++)
			for (int k = 0; k < min(K * K, 5); k++) tmp.push_back({ v[i][j][k].r, v[i][j][k].c });
	partial_sort(tmp.begin(), tmp.begin() + mCount, tmp.end());
	for (int i = 0; i < mCount; i++) mTop[i] = { tmp[i].r + 1, tmp[i].c + 1 };
}

int getHeight(Point mP) {
	mP.r--, mP.c--;
	return map[mP.r][mP.c] + update[mP.r / K][mP.c / K];
}

void work(Point mA, Point mB, int mH) {
	mA.r--, mA.c--, mB.r--, mB.c--;
	for (int i = mA.r / K; i <= mB.r / K; i++)
		for (int j = mA.c / K; j <= mB.c / K; j++)
			update[i][j] += mH;
}
```
