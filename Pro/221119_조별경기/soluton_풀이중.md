#if 1
#include <list>
#include <vector>
using namespace std;

#define MAXN 100003
int p[MAXN], r[MAXN], pScore[MAXN], gScore[MAXN];
list<int> List[MAXN];

int find(int x) {
	if (p[x] != x) p[x] = find(p[x]);
	return p[x];
}
void update(int x) {
	for (int i : List[x]) pScore[i] += gScore[x];
	gScore[x] = 0;
}

void unionSet(int x, int y) {
	x = find(x), y = find(y);
	if (r[x] < r[y]) update(x), swap(x, y);
	else update(y);
	p[y] = x;
	if (r[x] == r[y]) r[x]++;
	List[x].splice(List[x].end(), List[y]);
}
void init(int N){
	for (int i = 1; i <= N; i++) {
		List[i].clear(), List[i].push_back(i);
		p[i] = i, pScore[i] = gScore[i] = r[i] = 0;
	}
}

void updateScore(int mWinnerID, int mLoserID, int mScore){
	mWinnerID = find(mWinnerID), mLoserID = find(mLoserID);
	gScore[mWinnerID] += mScore, gScore[mLoserID] -= mScore;
}

void unionTeam(int mPlayerA, int mPlayerB){
	unionSet(mPlayerA, mPlayerB);
}

int getScore(int mID){
	int ret = pScore[mID] + gScore[find(mID)];
	return ret;
}
#endif // 1
