```cpp
/*
* union find (path compression + union by rank)
*/
#include<algorithm>
using namespace std;

const int LM = 100003;
int parent[LM], rnk[LM], score[LM];

void init(int N) {
	for (int i = 1; i <= N; i++)
		parent[i] = i, rnk[i] = score[i] = 0;
}

int find(int x) {
	if (x == parent[x]) return x;

	int root = find(parent[x]);

	if (root != parent[x]) {
		score[x] += score[parent[x]];
		parent[x] = root;
	}
	return root;
}

void updateScore(int mWinnerID, int mLoserID, int mScore) {
	score[find(mWinnerID)] += mScore;
	score[find(mLoserID)] -= mScore;
}

void unionTeam(int mPlayerA, int mPlayerB) {
	int a = find(mPlayerA);
	int b = find(mPlayerB);
	if (rnk[a] < rnk[b]) swap(a, b);

	parent[b] = a;
	score[b] -= score[a];
	if (rnk[a] == rnk[b]) rnk[a]++;
}

int getScore(int mID) {
	int root = find(mID);
	if (root == mID) return score[mID];
	return score[mID] + score[root];
}
```
