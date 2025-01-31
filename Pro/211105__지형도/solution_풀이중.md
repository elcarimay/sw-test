```cpp
#include <vector>
#include <algorithm>
#include <string.h>
#include <set>
using namespace std;

#define MAX_MAP_SIZE 2000

struct Point{
	int r, c;
};

struct Data {
	int r, c, h;
	bool operator<(const Data& re)const {
		if (h != re.h) return h > re.h;
		if (r != re.r) return r < re.r;
		return c < re.c;
	}
};

int (* map)[MAX_MAP_SIZE];
int update[63][63], N, K;
vector<Data> v[63][63];

void init(int N, int K, int mHeight[][MAX_MAP_SIZE]){
	map = mHeight;
	::N = N, ::K = K;
	memset(update, 0, sizeof(update));
	memset(v, 0, sizeof(v));
	//int num = min(K * K, 5);
	set<Data> tmp;
	int M = N / K;
	for (int r = 0; r < M; r++) for (int c = 0; c < M; c++) {
		tmp.clear();
		for (int i = r * K; i < r * K + K; i++)
			for (int j = c * K; j < c * K + K; j++)
				tmp.insert({ i, j, map[i][j] });
		int cnt = 1;
		for (set<Data>::iterator it = tmp.begin(); it != tmp.end(); it++) {
			v[r][c].push_back(*it);
			if (cnt++ == 5) break;
		}
	}
}

void destroy(){}

void query(Point mA, Point mB, int mCount, Point mTop[]){
	vector<Data> tmp;
	mA.r--, mA.c--, mB.r--, mB.c--;
	for (int i = mA.r / K; i <= mB.r / K; i++)
		for (int j = mA.c / K; j <= mB.c / K; j++)
			for (Data nx : v[i][j]) tmp.push_back({ nx.r, nx.c, nx.h + update[i][j] });
	partial_sort(tmp.begin(), tmp.begin() + mCount, tmp.end());
	for (int i = 0; i < tmp.size();i++) {
		mTop[i].r = tmp[i].r + 1, mTop[i].c = tmp[i].c + 1;
		if (i == mCount) return;
	}
}

int getHeight(Point mP){
	mP.r--, mP.c--;
	return map[mP.r][mP.c] + update[mP.r / K][mP.c / K];
}

void work(Point mA, Point mB, int mH){
	mA.r--, mA.c--, mB.r--, mB.c--;
	for (int i = mA.r / K; i <= mB.r / K; i++)
		for (int j = mA.c / K; j <= mB.c / K; j++)
			update[i][j] += mH;
}
```
