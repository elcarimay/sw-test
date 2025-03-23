```cpp
#if 1 // sqrt ver. 575 ms
#include <vector>
using namespace std;

#define MAXN 100003
#define MAX_TYPES 1003
#define MAXNN 320 // MAXN을 벗어나지 않는 최소 제곱근정도로 설정하면 됨

int* A;
int N, sq, sumA[MAXNN];
void build() {
	for (sq = 1; sq * sq < N - 1; sq++);
	for (int i = 0; i < N - 1; i++) {
		if (i % sq == 0) sumA[i / sq] = 0;
		sumA[i / sq] += A[i];
	}
}

int sum_query(int l, int r) {
	int sumv = 0;
	while (l <= r && l % sq) sumv += A[l++];
	while (l <= r && (r + 1) % sq) sumv += A[r--];
	while (l <= r) sumv += sumA[l / sq], l += sq;
	return sumv;
}

vector<int> roadType[MAX_TYPES];
void init(int N, int M, int mType[], int mTime[]) {
	::N = N, A = mTime;
	for (int i = 0; i < M; i++) roadType[i].clear();
	for (int i = 0; i < N - 1; i++)	roadType[mType[i]].push_back(i);
	build();
}

void destroy() {}

void update(int mID, int mNewTime) {
	sumA[mID / sq] -= A[mID];
	A[mID] = mNewTime;
	sumA[mID / sq] += mNewTime;
}

int updateByType(int mTypeID, int mRatio256) {
	int ret = 0;
	for (auto nx : roadType[mTypeID]) {
		update(nx, (int)(A[nx] * mRatio256 / 256));
		ret += A[nx];
	}
	return ret;
}

int calculate(int mA, int mB) {
	if (mA > mB) swap(mA, mB);
	return sum_query(mA, mB - 1);
}
#endif // 0
```
