```cpp
#if 1
#include <list>
#include <vector>
using namespace std;

#define MAXN 100003
int p[MAXN], r[MAXN], gScore[MAXN];

int find(int x) {
	if (p[x] == x) return x;
	int root = find(p[x]);
	if (root != p[x]) {
		gScore[x] += gScore[p[x]];
	}
	return p[x] = root;
}

void init(int N){
	for (int i = 1; i <= N; i++) 
		p[i] = i, gScore[i] = r[i] = 0;
}

void updateScore(int mWinnerID, int mLoserID, int mScore){
	gScore[find(mWinnerID)] += mScore, gScore[find(mLoserID)] -= mScore;
}

void unionTeam(int mPlayerA, int mPlayerB){
	int x = find(mPlayerA), y = find(mPlayerB);
	if (r[x] < r[y]) swap(x, y);
	gScore[y] = gScore[y] - gScore[x];
	p[y] = x;
	if (r[x] == r[y]) r[x]++;
}

int getScore(int mID){
	int root = find(mID);
	if (root == mID) return gScore[mID];
	int ret = gScore[mID] + gScore[root];
	return ret;
}
#endif // 1

```
