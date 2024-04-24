```cpp
#include<algorithm>
#include<vector>
#include<list>
using namespace std;

const int LM = 100003;

int gid[LM], score[LM], gScore[LM];
vector<int> group[LM];
//list<int> group[LM];

void init(int N) {
	for (int i = 1; i <= N; i++) {
		gid[i] = i, score[i] = gScore[i] = 0;
		group[i] = { i };
	}
}

void updateScore(int mWinnerID, int mLoserID, int mScore) {
	gScore[gid[mWinnerID]] += mScore;
	gScore[gid[mLoserID]] -= mScore;
}

void unionTeam(int mPlayerA, int mPlayerB) {
	int a = gid[mPlayerA], b = gid[mPlayerB];
	if (group[a].size() < group[b].size()) swap(a, b);

	for (int x : group[b]) {
		score[x] += gScore[b] - gScore[a];
		gid[x] = a;
		group[a].push_back(x);
	}
	//group[a].splice(group[a].end(),group[b]);			: list인 경우, splice 활용 가능
}

int getScore(int mID) {
	return score[mID] + gScore[gid[mID]];
}
```
