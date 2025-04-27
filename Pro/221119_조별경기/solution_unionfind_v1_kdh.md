```cpp
#if 1
// unionfind problem ver.1
// 개인별/그룹별 점수를 모두 관리함
#include <list>
#include <vector>
using namespace std;

#define MAXN 100003
int p[MAXN], r[MAXN], pScore[MAXN], gScore[MAXN];

int find(int x) {
	if (p[x] == x) return x;
	int root = find(p[x]);
	pScore[x] += pScore[p[x]];
	return p[x] = root;
}

void init(int N){
	for (int i = 1; i <= N; i++) 
		p[i] = i, pScore[i] = gScore[i] = r[i] = 0;
}

void updateScore(int mWinnerID, int mLoserID, int mScore){
	gScore[find(mWinnerID)] += mScore, gScore[find(mLoserID)] -= mScore;
}

void unionTeam(int mPlayerA, int mPlayerB){
	int x = find(mPlayerA), y = find(mPlayerB);
	if (r[x] < r[y]) swap(x, y);
	pScore[y] = gScore[y] - gScore[x];
	p[y] = x;
	if (r[x] == r[y]) r[x]++;
}

int getScore(int mID){
	int ret = pScore[mID] + gScore[find(mID)];
	return ret;
}
#endif // 1
```
